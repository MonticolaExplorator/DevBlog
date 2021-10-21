---
layout: post
title:  "Uploading files from a .NET 5 application to a Windows machine using Powershell Remote"
date:   2021-10-21 19:00:00 +0200
categories: [Net, Powershell]
tags: [.Net 5, Powershell, Powershell remoting]
---

I was faced the other day with the task of running some Powershell scripts on a remote Windows machine from a .NET 5 application. I decided to use [Powershell remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.1) to run the commands on the target machine, using [Microsoft.PowerShell.SDK nuget package](https://www.nuget.org/packages/Microsoft.PowerShell.SDK/) to host Powershell on the .NET 5 application.

So far, so good. But then I realized I also had to upload some configuration files to the remote computer. I thought that it would be great to upload these files using Powershell remoting itself, since that was already a requirement. It might be obvious, but it took me a while to find how to do this. And since I have not seen elsewhere sample code to do this, I am sharing what I found with you all.

> I actually found another example wich I can't find now. It uses Set-Content on the remote machine passing chunks of bytes read from the source file, but I feel that it is a little bit like reinventing the wheel and prefer using simple Powershell commands.

The easiest way to proceed when doing this kind of things is thinking how would you do this if you were using Powershell console directly and then translate it to code for your .NET application. And in Powershell, you would just create a remote session and run [Copy-Item](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/copy-item) with:

- Path: the path to the file you want to upload
- Destination: the destination on the remote computer where you want to place the file
- ToSession: the session on the remote computer, created with [New-PSSession](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/new-pssession) 

## The code

The method is very simple, and it is not intended to be as is on every scenario, it is just some sample code. You can find [the source code on GitHub](https://github.com/MonticolaExplorator/NetSamples/blob/0df09027d83a5085c8d45e82c5a51087bf81319a/source/NetSamples.PowerShell/PowerShellTools.cs#L24) as well.

~~~~c#
public static async Task UploadFileToAsync(string destinationComputer, PSCredential credential, FileInfo file, string destinationPath, CancellationToken cancelToken=default)
{
    if (!file.Exists)
        throw new FileNotFoundException($"File {file.FullName} does not exist");

    using var powerShell = System.Management.Automation.PowerShell.Create();
    powerShell.AddCommand("New-PSSession")
        .AddParameter("ComputerName", destinationComputer)
        .AddParameter("Credential", credential)
        .AddParameter("UseSSL")
        .AddParameter("ErrorAction", "Stop");

    var psSession = (await powerShell.InvokeAsync()).SingleOrDefault();
    try
    {
        cancelToken.ThrowIfCancellationRequested();
        powerShell.Commands.Clear();
        powerShell.AddCommand("Copy-Item")
            .AddParameter("Path",file.FullName)
            .AddParameter("Destination", destinationPath)
            .AddParameter("ToSession", psSession)
            .AddParameter("ErrorAction", "Stop");
        await powerShell.InvokeAsync();
    }
    finally
    {
        if (psSession != null)
        {
            powerShell.Commands.Clear();
            powerShell.AddCommand("Remove-PSSession")
                .AddArgument(psSession);
            await powerShell.InvokeAsync();
        }
    }
}
~~~~

And it works!!! Honestly, it does! You don't believe me? Here are some screenshots. Here on my local computer.

![TXT file on my local computer that I uploaded to a Windows machine](/assets/images/2021/october/uploading_files_from_net5app_to_windows_machine_usin_psremoting/localfile.png)

And now on the Windows server.

![TXT file on the remote Windows machine](/assets/images/2021/october/uploading_files_from_net5app_to_windows_machine_usin_psremoting/remotefile.png)

I know, I know. I might as well have copied the file myself using remote desktop, so the screenshots are worth nothing. But a blog post without images felt too sad.

## The code review

This is just sample code, but still I tried to follow some good practices.

### UseSSL

Have you noticed the _UseSSL_ switch when creating the session? With this switch the remote connection will be stablished over https. I recommend you to read [Security Considerations for PowerShell Remoting using WinRM](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/winrmsecurity?view=powershell-7.1) to understand the differences between http and https for WinRM. Read it but, to sum up:

- Even with http, communication is encrypted after initial authentication.
- With http you are exposed to vulnerabilities of the authentication protocol used (Kerberos or NTLM)
- In some cases with http the client can't authenticate the server and you have to add it to trusted hosts.

If you wan't to use https, you can easily [configure a self-signed certificate](https://cloudblogs.microsoft.com/industry-blog/en-gb/technetuk/2016/02/11/configuring-winrm-over-https-to-enable-powershell-remoting/) which at least will make all the comunications encrypted. Just remember that, if your certificate is self-signed, you will have to tell Powershell to skip verification of the certificate authority. 

~~~~powershell
$sessionOption = New-PSSessionOption -SkipCACheck
$psSession = New-PSSession -ComputerName $destinationComputer -Credential $credential -UseSSL -SessionOption $options -ErrorAction Stop
~~~~

### Error handling

If I had to use this on a real application, I would make sure that the method behaves as expected. That probably means that if finally the file is not copied to the destination on the remote computer, the method should raise an exception.

The problem is that non terminating errors don't make _InvokeAsync_ fail. In case you are not familiar with Powershell, most errors in Powershell are [non terminating](https://devblogs.microsoft.com/scripting/understanding-non-terminating-errors-in-powershell/). That is why I added _-ErrorAction Stop_ to the commands, that makes the errors terminating and in those cases _InvokeAsync_ throws an exception.

I would consider all possible scenarios. What happens if:

- the source file does not exist
- the file already exist on the destination
- the destination is unreachable
- the destination folder does not exist
- the destination drive does not exist
- if I call this method in a loop making it fail (would open sessions pile up?)

If you don't do this, you application might fail silently, and your users will blame you both for failing and for not giving a meaninfull error.

### Three roundtrips, why not AddScript?

If you play around with the [Microsoft.PowerShell.SDK nuget package](https://www.nuget.org/packages/Microsoft.PowerShell.SDK/) you will see that the _PowerShell_ class has an _AddScript_ method. You could compose the Powershell script and use _AddScript_ to run it all at once. Something like

~~~~c#
    public static async Task UploadFileToAsync(string destinationComputer, PSCredential credential, FileInfo file, string destinationPath, CancellationToken cancelToken = default)
    {
        if (!file.Exists)
            throw new FileNotFoundException($"File {file.FullName} does not exist");

        var copyToRemoteScrip = @"
$credential = $args[0];
$destinationComputer = $args[1];
$destinationPath = $args[2];
$sourcePath = $args[3];

$psSession = New-PSSession -ComputerName $destinationComputer -Credential $credential -UseSSL -ErrorAction Stop
try
{
    Copy-Item -Path $sourcePath -Destination $destinationPath -ToSession $psSession -ErrorAction Stop;
}
finally
{
    Remove-PsSession -Session $psSession;
}";

        using var powerShell = System.Management.Automation.PowerShell.Create();
        powerShell.AddScript(copyToRemoteScrip)
            .AddArgument(credential)
            .AddArgument(destinationComputer)
            .AddArgument(destinationPath)
            .AddArgument(file.FullName);

        await powerShell.InvokeAsync();
    }
~~~~

It took me a veeeeery long time to discover that you can pass arguments to a script. I thought it was not possible until I found [this answer on StackOverflow](https://stackoverflow.com/a/61893812/7747594).


### Async all the way down

For operations that will block the thread until some other operation is finished (database queries, disk I/O, web requests...) look for async API and make your code async too. Use cancellation tokens as well. In this case, _InvokeAsync_ method does not support cancellation token. Still, in case new versions accept a cancellation token, migrating the code will be easier if your function recieves a cancellation token itself since day one. If this code was not for self consume, I would consider not to expose the cancellation token if I don't honour it.


And that was it. My second blog post took a lot of effort. Keep coding so we have a lot of code base to maintain by the time machines are able to code themselves!

