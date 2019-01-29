---
layout: post
title:  "Stacktraces and deinitializers in Swift"
date:   2019-01-17 13:00 -0500
comments: true
---

While continuing to make my way through
[the Swift Book](https://docs.swift.org/swift-book/), I finally reached
the definition of `Deinitialization`.  The authors wait until almost
halfway through the pagecount before introducing this concept.  Since
this is the first time in the book a code sample has code that is called
by the runtime rather than the reader, I wanted to print a stack trace
in the deinitializer and see what it looked like.

Here is the `Deinitialization` example from the Swift Book with my call
to print the stack trace on line 25.  I had to add the `import
Foundation` to make it compile with that line in there.

<div class="java" style="font-family:monospace;"><ol><li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #000000; font-weight: bold;">import</span> <span style="color: #006699;">Foundation</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #000000; font-weight: bold;">class</span> Bank <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">static</span> var coinsInBank <span style="color: #339933;">=</span> 10_000</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">static</span> func distribute<span style="color: #009900;">&#40;</span>coins numberOfCoinsRequested<span style="color: #339933;">:</span> Int<span style="color: #009900;">&#41;</span> <span style="color: #339933;">-&gt;</span> Int <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; let numberOfCoinsToVend <span style="color: #339933;">=</span> min<span style="color: #009900;">&#40;</span>numberOfCoinsRequested, coinsInBank<span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; coinsInBank <span style="color: #339933;">-=</span> numberOfCoinsToVend</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">return</span> numberOfCoinsToVend</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #000000; font-weight: bold;">static</span> func receive<span style="color: #009900;">&#40;</span>coins<span style="color: #339933;">:</span> Int<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; coinsInBank <span style="color: #339933;">+=</span> coins</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #000000; font-weight: bold;">class</span> Player <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; var coinsInPurse<span style="color: #339933;">:</span> Int</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; init<span style="color: #009900;">&#40;</span>coins<span style="color: #339933;">:</span> Int<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; coinsInPurse <span style="color: #339933;">=</span> Bank.<span style="color: #006633;">distribute</span><span style="color: #009900;">&#40;</span>coins<span style="color: #339933;">:</span> coins<span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; func win<span style="color: #009900;">&#40;</span>coins<span style="color: #339933;">:</span> Int<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; coinsInPurse <span style="color: #339933;">+=</span> Bank.<span style="color: #006633;">distribute</span><span style="color: #009900;">&#40;</span>coins<span style="color: #339933;">:</span> coins<span style="color: #009900;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; deinit <span style="color: #009900;">&#123;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; debugPrint<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;PlayerOne has left the game&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <a href="http://www.google.com/search?hl=en&amp;q=allinurl%3Athread+java.sun.com&amp;btnI=I%27m%20Feeling%20Lucky"><span style="color: #003399;">Thread</span></a>.<span style="color: #006633;">callStackSymbols</span>.<span style="color: #000000; font-weight: bold;">forEach</span><span style="color: #009900;">&#123;</span>print<span style="color: #009900;">&#40;</span>$<span style="color: #cc66cc;">0</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; Bank.<span style="color: #006633;">receive</span><span style="color: #009900;">&#40;</span>coins<span style="color: #339933;">:</span> coinsInPurse<span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #009900;">&#125;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #009900;">&#125;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">var playerOne<span style="color: #339933;">:</span> Player<span style="color: #339933;">?</span> <span style="color: #339933;">=</span> Player<span style="color: #009900;">&#40;</span>coins<span style="color: #339933;">:</span> <span style="color: #cc66cc;">100</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">print<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;A new player has joined the game with <span style="color: #000099; font-weight: bold;">\(</span>playerOne!.coinsInPurse) coins&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #666666; font-style: italic;">// Prints &quot;A new player has joined the game with 100 coins&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">print<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;There are now <span style="color: #000099; font-weight: bold;">\(</span>Bank.coinsInBank) coins left in the bank&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #666666; font-style: italic;">// Prints &quot;There are now 9900 coins left in the bank</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">playerOne<span style="color: #339933;">!</span>.<span style="color: #006633;">win</span><span style="color: #009900;">&#40;</span>coins<span style="color: #339933;">:</span> <span style="color: #cc66cc;">2</span>_000<span style="color: #009900;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">print<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;PlayerOne won 2000 coins &amp; now has <span style="color: #000099; font-weight: bold;">\(</span>playerOne!.coinsInPurse) coins&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #666666; font-style: italic;">// Prints &quot;PlayerOne won 2000 coins &amp; now has 2100 coins&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">print<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;The bank now only has <span style="color: #000099; font-weight: bold;">\(</span>Bank.coinsInBank) coins left&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #666666; font-style: italic;">// Prints &quot;The bank now only has 7900 coins left</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">playerOne <span style="color: #339933;">=</span> nil</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #666666; font-style: italic;">// Prints &quot;PlayerOne has left the game&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">print<span style="color: #009900;">&#40;</span><span style="color: #0000ff;">&quot;The bank now has <span style="color: #000099; font-weight: bold;">\(</span>Bank.coinsInBank) coins&quot;</span><span style="color: #009900;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #666666; font-style: italic;">// Prints &quot;The bank now has 10000 coins</span></div></li>
</ol></div>

I learned about this call to `Thread.callStackSymbols.forEach` on
[stackoverflow](https://stackoverflow.com/questions/30754796/how-to-print-call-stack-in-swift/30814498),
but the more interesting information is in the reference docs for
[`Thread`](https://developer.apple.com/documentation/foundation/thread).
You see, `callStackSymbols` is an array of `String` and therefore it has
a `forEach`.  The output from running the above program looks like this:

<div class="text" style="font-family:monospace;"><ol><li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">A new player has joined the game with 100 coins</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">There are now 9900 coins left in the bank</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">PlayerOne won 2000 coins &amp; now has 2100 coins</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">The bank now only has 7900 coins left</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&quot;PlayerOne has left the game&quot;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">0 &nbsp; ??? &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x00000001153071e5 0x0 + 4650463717</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">1 &nbsp; ??? &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x0000000115307478 0x0 + 4650464376</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">2 &nbsp; libswiftCore.dylib &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;0x00000001179bda00 _swift_release_dealloc + 16</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">3 &nbsp; ??? &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x00000001153068d5 0x0 + 4650461397</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">4 &nbsp; swift &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x000000010e8a723d _ZN4llvm5MCJIT11runFunctionEPNS_8FunctionENS_8ArrayRefINS_12GenericValueEEE + 365</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">5 &nbsp; swift &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x000000010e8adc1c _ZN4llvm15ExecutionEngine17runFunctionAsMainEPNS_8FunctionERKNSt3__16vectorINS3_12basic_stringIcNS3_11char_traitsIcEENS3_9allocatorIcEEEENS8_ISA_EEEEPKPKc + 1004</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">6 &nbsp; swift &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x000000010db0efe4 _ZL14performCompileRN5swift16CompilerInstanceERNS_18CompilerInvocationEN4llvm8ArrayRefIPKcEERiPNS_16FrontendObserverEPNS_20UnifiedStatsReporterE + 52660</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">7 &nbsp; swift &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x000000010dafed35 _ZN5swift15performFrontendEN4llvm8ArrayRefIPKcEES3_PvPNS_16FrontendObserverE + 7717</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">8 &nbsp; swift &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x000000010daa4965 main + 1349</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">9 &nbsp; libdyld.dylib &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 0x00007fff79108015 start + 1</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">The bank now has 10000 coins</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
</ol></div>

Line 14 of the output, `8 swift ... main + 1349`, corresponds exactly to
1349 characters after the start of above code listing, which is
`playerOne = nil`.  And indeed that is when the deinitializer is called.

Looking at the reference documentation, there are some other interesting
properties of `Thread`.  Some useful class properties:

* isMultiThreaded: Boolean
* isMainThread: Boolean
* name: the name of the thread
* current: the current thread

Let's add this line to the beginning of the code listing:

```
Thread.current.name = "Main Game Thread"
```

Modifying the above code to print out these properties, the new output
is:

```
isMultiThreaded: false
current.isMainThread: true
current.name: Main Game Thread
```

So we can see that the deinitializer is called on the same thread as the
main code.

I'm sure there is more to reveal about runtime stack inspection, but
mastering the diagnostic and debugging techniques of a programming
language platform is an important component of proficiency.  It pays to
pick up and save such tips when they come along.
