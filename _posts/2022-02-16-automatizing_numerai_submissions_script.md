---
layout: post
title:  "Automatizing Numerai submissions (Python + Docker + Azure): sanitizing the submission script"
date:   2022-02-15 21:00:00 +0100
categories: [Numerai]
image:
  src: /assets/images/2022/february/py_docker_azure_nmr.jpg
  width: 400   # in pixels
  alt: "My automatization idea on a napkin: python + docker + Azure = Automatic Numerai submissions"
tags: [Numerai, Automatic Numerai submissions, Azure, docker, python]
---

I could not find an icon for this series of posts. I wanted to compose one out of other icons, but I am not that fluent with paint. So I decided to sketch it myself. I promise to, at least take a picture of it with better ilumination. 

If you are anything like me and you are into [Numerai](https://numer.ai/tournament), you probably have a buch of python files, mostly notebooks, for analyzing and creating models. You also have files for your models. And you have all those files versioned somewhere. And, while everything is mostly all over the place, you at least have a very simple python script for your weekly submissions. If you are exactly like me, at least we don't have our Numerai keys on source code.

Having to run yourself some code every Saturday or Sunday to upload your predictions feels sad in this blockchain, data driven, intelligent world. So my next step on this Numerai journey will be to properly automatice my submissions. I think a proper solution for this has to be:

- Automatic: it has to run without any human interaction every week
- Reliable
- Easy to update when I create new models
- Easy to run localy in case something goes wrong
- Inexpensive: we are here for the ~money~ fun!!!11 Spending money is not fun.

I am probably missing some requirements. What I am going to try is using Azure Container Instances to run a container once a week to make the predictions. It is one way, there are probably others. Anyway, having a clean script that you run locally is a good start. The first step is refactor a little bit the submission script to make it container and cloud friendly. The checklist for this task is:

- Parametrize secrets, including your Numerai credentials
- Parametrize the directories where your models and config files are
- Refactor so you download any unnecessary file or predict an already submitted model

## Parametrize secrets

Secrets don't belong in source code. So, how do yo provide your script with your Numerai credentials? Easy, just read them from [environment variables](https://12factor.net/config). 

~~~~python
import numerapi
import os

numeraiUserName = os.environ["NumeraiUserName"]
numeraiAccessKey = os.environ["NumeraiAccessKey"]
napi=numerapi.NumerAPI(numeraiUserName, numeraiAccessKey)
~~~~

If you run the script on your computer, you just set the variables beforehand. When we create the container and put it on the cloud, we will see the best way to pass this secrets as environment variables. Who said it was going to be hard?

## Parametrize the directories where your models and config files are

This one is not hard either. When you run your script on your local machine, the files that you usually read are wherever you place them. _c:\dev\nrm\models\finalversion.h5_, _../../config/variables.pkl_, _./whatever.json_ , you name it. The problem is that when you run this script on a container, these files will probably be somewhere else. Same goes for the data that you download from Numerai (parquets, csv, etc.).

If you are organized, you probably can identify a root where your models and config files are. If not, I urge you to refactor your code and reorganice your files, at least consider it ;). In the end, the location of every file that you read or write must be relative to some configurable directory. In my case, what I will do is configure a root for models and config files and a root for data I download from Numerai. So, first, I read these two root directories from environment variables.

~~~~python
dataDirectory = os.environ["Numerai_DataDir"]
modelsDirectory = os.environ["Numerai_ModelsDir"]
~~~~

Then, I just had to refactor my code to make read all my files from _modelsDirectory_. Changin lines like this

~~~~python
featuresFilePath = '..\\models\\features.json';
~~~~

for 

~~~~python
featuresFilePath = os.path.join(modelsDirectory 'features.json')
~~~~

>_os.path.join_ will compose the path taking into account the directory separator of the operating system where you run the script, thus making your code more portable

And the same for the files I download. From Numerai. For instance:

~~~~python
dataFolder= f'..\\data\\numerai_dataset_{current_round}'
tournamentFile = f'{dataFolder}\\numerai_tournament_data.csv'
napi.download_current_dataset(dest_path='..\\data\\')

newTournamentFile = '..\\data\\numerai_tournament_data.parquet'
napi.download_dataset(filename = 'numerai_tournament_data.parquet', dest_path= newTournamentFile)
~~~~

for

~~~~python
legacyCSVFile = os.path.join(os.path.join(dataDirectory, 'legacy'), 'numerai_tournament_data.csv')
napi.download_current_dataset(dest_path=dataDirectory, dest_filename='legacy')
tournamentParquetFile = os.path.join(dataDirectory, 'numerai_tournament_data.parquet');
napi.download_dataset(filename = 'numerai_tournament_data.parquet', dest_path= tournamentParquetFile)
~~~~

> Note that on the refactorized version of the code we pass *dest_filename='legacy'* when downloading the csv so we get a fixed name, instead of one with the number of the round on it

> Since I intend to run this on a container, I have not included code to cleanup files when the script finishes.

## Don't do unnecessary things

Don't download a big parquet file if you are not going to use it. Don't submit a model that has been already submitted. Mostly because, if for any reason your code times out, you will be better off if the next time you continue where you left it. Arguably also because it can save you costs of doing things more than once.

My code is very primitive and straighforward, I don't do anything fancy. First, I divide my models into _legacy_ and _massive_, then loop over them to see which ones are missing and download the corresponding data file.

~~~~python
anasPlatyrhynchosModelName="anas_platyrhynchos"
nettaRufinaModelName = "netta_rufina"
aythyaFerinaModelName = "aythya_ferina"
melanittaNigraModelName = "melanitta_nigra"
rhodonessaModelName = "rhodonessa_caryophy"
corvusCoroneModelName = "corvus_corone"

legacyModels = [anasPlatyrhynchosModelName, nettaRufinaModelName, aythyaFerinaModelName, rhodonessaModelName]
massiveModels = [melanittaNigraModelName, corvusCoroneModelName]

waitingLegacyModels = []
waitingMassiveModels = []

#legacy
downloadedLegacyData = False
legacyCSVFile = os.path.join(os.path.join(dataDirectory, 'legacy'), 'numerai_tournament_data.csv')

for legacyModel in legacyModels:
    submission=napi.get_submission_filenames(round_num=current_round,model_id=numeraiModels[legacyModel])
    if(len(submission)==0):
        waitingLegacyModels+=[legacyModel]
        if(not downloadedLegacyData):
            napi.download_current_dataset(dest_path=dataDirectory, dest_filename='legacy')
            downloadedLegacyData = True;

#Massive
tournamentParquetFile = os.path.join(dataDirectory, 'numerai_tournament_data.parquet');
downloadedMassiveData = False

for massiveModel in massiveModels:
    submission=napi.get_submission_filenames(round_num=current_round,model_id=numeraiModels[massiveModel])
    if(len(submission)==0):
        waitingMassiveModels+=[massiveModel]
        if(not downloadedMassiveData):
            napi.download_dataset(filename = 'numerai_tournament_data.parquet', dest_path= tournamentParquetFile)
            downloadedMassiveData = True
~~~~

>I haven not found a better way of checking if a model is waiting for submissions than using *get_submission_filenames* for the current round. There has to be a better way, but this one works.

Finally, each of my model submissions are protected with code that checks if the model is waiting for submissions. For instance:

~~~~python
if(anasPlatyrhynchosModelName in waitingLegacyModels):
    uploadLegacyModel(legacyCSVFile,legacyFeatureNames, napi, numeraiModels[anasPlatyrhynchosModelName], os.path.join(modelsDirectory,'model_regression.h5'))
~~~~

>I would really want to provide you with a repository with all this code. Unfortunately, I don't have time to. This comes from my personal code which I don't want to give away :D. If you are advanced enough, the idead I give here are probably obvious, you might even correct my suggestions. If you are not, maybe you are not able to start off without sample code. In both cases, I am sorry.

In my case, I betray the rule of not doing things more than once just a little bit. I load csv or parquet file every time a predict a model. Just in case on of my models unadvertedly modifies the data in memory.


And that was it. Next time, we will be looking at how to create a container for our script. Until then, may the true contribution be with you. 