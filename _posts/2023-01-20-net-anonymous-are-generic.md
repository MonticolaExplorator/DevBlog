---
layout: post
title:  "C# .NET anonymous types are generic, and I don't know why"
date:   2023-01-20 10:00:00 +0100
categories: [NET, C#]
image:
  src: /assets/images/2023/january/net-anonymous-are-generic/co.png
  width: 200  # in pixels
  alt: "C#"
tags: [C#,.NET, anonymous types]
---

I recently learnt that the C# compiler for .NET 6 compiles anonymous types into generic types. I have not tested it, but my guess is that this is also true on .NET Framework. As far as I know, that is an undocumented feature that should not be relied upon. There must be a good reason for this decision, since most aspects of a language or a compiler are thoughtfully architected.

## What are anonymous types?

But, what are anonymous types anyway? Well, the name does not suggest much: types with no name and so you cannot make reference to them at source code level. It is funny that such a nice feature of the C# language has a name that only highlights its limitations. Anonymous types provide a way to implicitly declare types with a sentence that also creates an instance of the type. For example:

~~~~c#
var value = new {Amount = 0.1m, Currency = "Eur"};
Console.WriteLine($"Cost is {value.Amount} {value.Currency}");
~~~~

On the previous sentence, we are creating a _value_ object that has an _Amount_ and a _Currency_ properties, and we can make reference to those properties later in the code. But dit not have to explicitly declare a type. This feature is specially handy when working with [LINQ](https://learn.microsoft.com/en-us/dotnet/csharp/linq/). For instance, if we have a collection of bank customers with _Expense_ and _Income_ and we want to select the balance for each one, we can simply do:

~~~~c#
var balances = customers.Select(c => new {Balance = c.Income - c.Expense, Currency = c.Currency})
~~~~

Another place where anonymous type shine is at [creating composite keys for LINQ operations like join](https://learn.microsoft.com/en-us/dotnet/csharp/linq/perform-inner-joins#example---composite-key-join). For instance, imagine an airline company that has lost baggage inquiries, that include brand and color of the baggage, and also a list of lost and found baggages. They could try to cross both lists to try to find the owner of lsot luggage belonging:

~~~~c#
var matches = inquiries.Join(lostLuggages, i => new { Color = i.Color, Brand = i.Brand }, l => new { Color = l.Color, Brand = l.Brand }, (i, l) => new { Inquiry = i, Luggage = l });
~~~~

The _matches_ result will be a enumeration that has every match of each inquiry to each lost luggage of the same color and brand. Notice how we used anonymous types on the creation of inner and outter keys.

These brief examples are enough to see where anonymous types are useful. They are useful when we simply want to encapsulate a handfull of properties, and the resulting type is almost local to a function, i.e. it is a temporary construct to achieve the final result and has no meaning anywhere else in the code. If we had to explicitly declare the anonymous types used on our previous examples, the code would be dreadfully verbose and boilerplate.

Now we can start making ourselves questions.

## What does the compiler do with these expressions?

Well, the answer could be: which compiler? Maybe it is better to ask what should the compiler do with anonymous object creation expressions. The answer is on the [C# language specification](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/expressions#117157-anonymous-object-creation-expressions). It can be a little bit overwhelming, but it is worth it to give it a shoot and try to see what we can make out of that.

> An anonymous object initializer declares an anonymous type and returns an instance of that type. An anonymous type is a nameless class type that inherits directly from object. The members of an anonymous type are a sequence of read-only properties inferred from the anonymous object initializer used to create an instance of the type. Specifically, an anonymous object initializer of the form new { p₁ = e₁ , p₂ = e₂ , … pᵥ = eᵥ } declares an anonymous type of the form:
~~~~c#
class __Anonymous1
{
    private readonly «T1» «f1»;
    private readonly «T2» «f2»;
    ...
    private readonly «Tn» «fn»;

    public __Anonymous1(«T1» «a1», «T2» «a2»,..., «Tn» «an»)
    {
        «f1» = «a1»;
        «f2» = «a2»;
        ...
        «fn» = «an»;
    }

    public «T1» «p1» { get { return «f1»; } }
    public «T2» «p2» { get { return «f2»; } }
    ...
    public «Tn» «pn» { get { return «fn»; } }
    public override bool Equals(object __o) { ... }
    public override int GetHashCode() { ... }
}
~~~~

> where each «Tx» is the type of the corresponding expression «ex»

As we said, these sentences implicitly declare a type and also return an instance of that type. The resulting type is nameless, thus it can be referenced from source code. It's members are read only properties, [Eric Lippert provided fabulous insight into the reason why they decided to make the properties read only on an answer to a StackOverflow question](https://stackoverflow.com/a/9065678/7747594).

Another interesting piece of the specification is:

> Within the same program, two anonymous object initializers that specify a sequence of properties of the same names and compile-time types in the same order will produce instances of the same anonymous type.

So, to make it short, the compiler will be creating a new nameless class for us.

## Why does not the language allow to give anonymous types a name?

Probably stupid question since they will no longer be anonymous. So for this one, let's assume they are implicitly defined types. We could have

~~~~c#
var balances = customers.Select(c => new class PaymentsBalance{Balance = c.Income - c.Expense, Currency = c.Currency})
~~~~

And then use it elsewhere in the code. But, what would be the visibility of that class? What would be the namespace? My guess is that it is not worth it and they don't want the language to go in that direction. If the type is important enough that you will be giving it a name, you should be ok having to define it explicitly.

## Can I create an anonymous type dynamically?

Yes, there are questions on StackOverflow with similar titles (see [here](https://stackoverflow.com/questions/4024754/creating-an-anonymous-type-dynamically) and [here](https://stackoverflow.com/questions/3740021/how-to-dynamic-new-anonymous-class)). 

We know that anonymous type are a language feature, and that the compiler must create a type for our anonymous object creation sentences. Can you compile C# language dynamically? [Yes](https://www.nuget.org/packages/Microsoft.CSharp#readme-body-tab). In your dynamic code you can make use of anonymous types the same way you would do on your static code. But if your goal is creating classes dynamically, you will probably have to create nominal types dynamically.

## Can I tell if a type is an anonymous type?

No, you can't. If your code depends on detecting anonymous types, you are probably doing something wrong or you have cornered yourself very badly. If you absolutely have to check wheter or not a type is anonymous, I would take a look at what [Jon Skeet](https://stackoverflow.com/a/315186/7747594) and [Marc Gravell](https://stackoverflow.com/a/315152/7747594) have to say on the matter. But, beware:

> Basically this is all pretty fragile...

## Why are anonymous type generic?

From all that we know about anonymous type, one would not say that they are created as generic types, but they are. Going back to one of our previous examples:

~~~~c#
var value = new {Amount = 0.1m, Currency = "Eur"};
Console.WriteLine($"Cost is {value.Amount} {value.Currency}");
~~~~

If we decomplile the assembly of the previous code, we will see the next code:

~~~~c#
[CompilerGenerated]
[DebuggerDisplay("\\{ Amount = {Amount}, Currency = {Currency} }", Type = "<Anonymous Type>")]
internal sealed class Ef__AnonymousType0<CAmountj__TPar, CCurrencyj__TPar>
{
  [DebuggerBrowsable(DebuggerBrowsableState.Never)]
  private readonly CAmountj__TPar CAmountEi__Field;
  [DebuggerBrowsable(DebuggerBrowsableState.Never)]
  private readonly CCurrencyj__TPar CCurrencyEi__Field;

  public CAmountj__TPar Amount
  {
    get
    {
      return this.CAmountEi__Field;
    }
  }

  public CCurrencyj__TPar Currency
  {
    get
    {
      return this.CCurrencyEi__Field;
    }
  }

  [DebuggerHidden]
  public CEf__AnonymousType0(
    CAmountj__TPar Amount,
    CCurrencyj__TPar Currency)
  {
    base.ctor();
    this.CAmountEi__Field = Amount;
    this.CCurrencyEi__Field = Currency;
  }

  [DebuggerHidden]
  public override bool Equals(object value)
  {
    var data = value as CEf__AnonymousType0<CAmountj__TPar, CCurrencyj__TPar>;
    if (this == data)
      return true;
    return data != null && EqualityComparer<CAmountj__TPar>.Default.Equals(this.CAmountEi__Field, data.CAmountEi__Field) && EqualityComparer<CCurrencyj__TPar>.Default.Equals(this.CCurrencyEi__Field, data.CCurrencyEi__Field);
  }

  [DebuggerHidden]
  public override int GetHashCode()
  {
    return (7296041 * -1521134295 + EqualityComparer<CAmountj__TPar>.Default.GetHashCode(this.CAmountEi__Field)) * -1521134295 + EqualityComparer<CCurrencyj__TPar>.Default.GetHashCode(this.CCurrencyEi__Field);
  }

  [DebuggerHidden]
  public override string ToString()
  {
    object[] objArray = new object[2];
    CAmountj__TPar amountIField = this.CAmountEi__Field;
    ref CAmountj__TPar local1 = ref amountIField;
    objArray[0] = (object) ((object) local1 != null ? local1.ToString() : (string) null);
    CCurrencyj__TPar currencyIField = this.CCurrencyEi__Field;
    ref CCurrencyj__TPar local2 = ref currencyIField;
    objArray[1] = (object) ((object) local2 != null ? local2.ToString() : (string) null);
    return string.Format((IFormatProvider) null, "{ Amount = {0}, Currency = {1} }", objArray);
  }
}
~~~~

> Depending on where you declare your anonymous, you might have to search for it on a different namespace

> If you are using [JetBrains dotPeek](https://www.jetbrains.com/decompiler/), make sure to enable Tools\Options\Decompiler\Show compiler-generated code or you wont see anonymous types

So there you have it. Lots of interesting things going on, and one of them is that the type is generic. It has one type parameter for each one of its properties. But why, oh, why? Well, it seems the reason has to do with complying with C# rules about visibility of types in some situations. [Eric Lippert explained it](https://ericlippert.com/2010/12/20/why-are-anonymous-types-generic/) with a post on his blog. Go ahead and read it, because I would not be able to explain it any better. I love his blog by the way, it is an invaluable source of inspiration. And [he likes birds](https://ericlippert.com/2020/12/15/backyard-birds-of-seattle/) like me!

Until next time, stay safe. Stay fun!
