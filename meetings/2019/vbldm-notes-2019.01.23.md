# Visual Basic Language Design Meeting

January 23, 2019

(Long meeting because several missed for OOF)

## Agenda

* [#357](https://github.com/dotnet/vblang/issues/357) Allow directives in the middle of VB statements. Paul1956 wants to know whether to prototype.
* [#355](https://github.com/dotnet/vblang/issues/355) Nullable Reference without flow analysis?.
* Discussion: Should we continue to have a different VS menu experience/VB user in Visual Studio? Recommend against or label as "traditional"?
* Next round of pattern matching.

## [#357](https://github.com/dotnet/vblang/issues/357) Allow directives in the middle of VB statements. Paul1956 wants to know whether to prototype

From an LDM, strict language perspective, this is a fine idea. There is value although people have been working around it for a long time.

From an implementation/compiler perspective, we're not sure how deep things go here. The problem is that we would apparently be changing the meaning of the new line delimiter. Are there places that some assumption was made that will be changed by adding directives. The problem is two-fold: any actual issues arising and having confidence that there are no issues, no overlooked side cases, since this is so foundational. 

OTOH, we have not looked at this ourselves, and the fix may be less deep than we fear.

Thanks to Paul for looking into this!
 
Summary: If someone submits a PR on this, we would not reject it for language reasons. We may need to reject it because we think it has adverse or far reaching issues, or the nature of the changes are such that we feel we can't have confidence on the impact.

## [#355](https://github.com/dotnet/vblang/issues/355) Nullable Reference Types without flow analysis?

Explore whether it's worth keeping on the table doing declaration only (no flow analysis) in VB.

We are concerned that allowing the metadata attributes for NRT to be used in code would block future work on NRT in VB, so we're going to prohibit their use. This will avoid a set of breaking changes later. 

The thread on this is super interesting as people look at a variety of thoughts around NRT. Since this work is a long way off, we didn't get very specific, but aren't sure skipping flow analysis results in a good experience that will integrate with C# NRT flow. The cool thing is that when we can look at this, we will have this thread to review - so keep the conversation going!

## Should we continue to have a different VS menu experience/VB user in Visual Studio? Recommend against or label as "traditional"?

A docs error pointed otu that there is a split experience between VB and C# users. Is this still a good thing? Is it challenging for VB developers to read non-Microsoft docs that describe C# and general menu usage in VB. 

* We want to think carefully about a change that would be this impactful.
* We need to determine how many users select "Visual Basic" developer.
* That choice affects both menus and keybindings
* Keybindings aren't "free" in that default keybindngs may block use of a key that would otherwise make sense.
* But keybindings are really hard for people to change. 

No action at this time. 

## Pattern matching - proposal

We should probably do some updates to the pattern notes that are evolving. The community is doing great, and we have time, so partner on moving closer to a proposal? Any proposal will stay open until we start the work. A few notes on docs:

* "When" is not part of the pattern but a part of the `Case` statement. For `If` it would have the same effect as `AndAlso`.
* If the precedence is the same as something that exists, you can use that (relational?) (update docs)

## Pattern matching - How do we tell the differences between a method call and a pattern that decomposes to a single value tuple?

To support the ongoing conversation, we'll report our noodling, which have not yet resulted in strong opinions.

C# and VB differ here because C# only allowed constants in case statements. 

What does M(x) mean here?

```vb
Case =3
Case M(x)
Case Matches M(x)
```

Potentially resolved by sometimes requiring Matches as in the last. 

```vb
Select Case obj
   Case x As String    ' Benefit of looking like a declaration
   Case String Into x  ' Reads left to right
   Case (1, 2)         ' tuple match or expression that produces tuple?
   Case (x, y)         ' Is this deconstruction or producing a tuple?
```

If we don’t allow mixing of expressions and patterns, maybe

```vb
Case (x,y)     ' Deconstruction
Matches (x,y)  ' Match
```

Select Case - expressions
Select Matches - patterns. Consider relational patterns like <3.

Or

```vb
Case (Dim x, Dim y) // Deconstruction
Case Dim (x, y) // Deconstruction
Case (x, y) // If this was supported, it would be a match against a tuple that first value matches x and second value matches y
```

Did not do any issue review. 
