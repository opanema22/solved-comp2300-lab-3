Download Link: https://assignmentchef.com/product/solved-comp2300-lab-3
<br>
Before you attend this week’s lab, make sure:

<ol>

 <li>you understand how simple assembly instructions are executed by the discoboard (and can read the cheat sheet to figure out what they do)</li>

 <li>you’re able to follow the execution of your program in the debugger, and inspect registers to see what’s going on</li>

 <li>you have read the laboratory text below</li>

</ol>

In this week’s lab you will:

<ol>

 <li>translate simple mathematical expressions into sequences of assembly instructions</li>

 <li>watch the status register and monitor the condition flags</li>

 <li>branch (jump) around in your program, including conditional branches</li>

</ol>

Useful reference material:

<ul>

 <li><a class="acton-tabs-link-processed" href="https://cs.anu.edu.au/courses/comp2300/v_media/manuals/ARMv7-cheat-sheet.pdf">ARM assembly cheat sheet</a></li>

 <li><a class="acton-tabs-link-processed" href="https://cs.anu.edu.au/courses/comp2300/v_media/manuals/ARMv7-M-architecture-reference-manual.pdf">ARM®v7-M Architecture Reference Manual</a></li>

</ul>

And here’s the conversion widget again, because it always comes in handy:

<table class="conversion-widget">

 <tbody>

  <tr>

   <td class="basetype">Decimal</td>

   <td><input id="dec_val" type="text" value="0"></td>

  </tr>

  <tr>

   <td class="basetype">Hex</td>

   <td><input id="hex_val" type="text" value="0x 0000 0000"></td>

  </tr>

  <tr>

   <td class="basetype">Binary</td>

   <td><input id="bin_val" type="text" value="0b 0000 0000 0000 0000 0000 0000 0000 0000"></td>

  </tr>

 </tbody>

</table>

<h2 id="introduction">Introduction</h2>

<a class="acton-tabs-link-processed" href="https://en.wikipedia.org/wiki/Pok%C3%A9mon#Gameplay_of_Pok%C3%A9mon">Pokemon</a> is a game where you play as a “pokemon trainer” and assemble a team of fictional creatures, for battle–against wild pokemon (to capture), and friends/foes (to prove your superiority). In this week’s lab, you’re going to create your very own game—a (much) simplified version of Pokemon.

In this game, you will have a pokemon character take turns with an enemy pokemon in an epic battle until one of the two pokemon runs out of hit points (HP) and faints! This isn’t a game for the faint of heart… You will start by creating your own pokemon and then we will add in the enemy pokemon later.

<p class="talk-box">It’s important to ask for help when you get stuck in your lab. If you’re an on-campus student: wave to one of your neighbours. If you’re online: say hey in your Teams chat! Ask what their favourite card game is, and whether they’ve ever thought of writing a computer-program version of it?

Fork &amp; clone the <a class="acton-tabs-link-processed" href="https://gitlab.cecs.anu.edu.au/comp2300/2021/comp2300-2021-lab-3">lab 3 template</a> to your machine and let’s get started.

<h2 id="exercise-1-creating-your-pokemon">Exercise 1: Creating your pokemon</h2>

In the lab 3 template you will find the following code already provided to get you started.

<pre><code class="language-ARM hljs"><span class="hljs-symbol">.syntax</span> unified<span class="hljs-symbol">.global</span> main<span class="hljs-symbol">.type</span> main, %<span class="hljs-meta">function</span><span class="hljs-symbol">main</span>:  <span class="hljs-comment">@ b exercise_3</span>  <span class="hljs-comment">@ Your code goes here</span><span class="hljs-comment">@ a simple infinite loop</span><span class="hljs-symbol">end_loop</span>:  <span class="hljs-keyword">nop</span>  <span class="hljs-keyword">b </span>end_loop<span class="hljs-symbol">.size</span> main, .-main<span class="hljs-comment">@ a place for your to complete exercise 3. uncomment line 6 when you're ready to start working on it!</span><span class="hljs-symbol">exercise_3</span>:<span class="hljs-symbol">...</span></code></pre>

<p class="think-box">But before you write any more code, think: what are the <em>minimum</em> features that you’ll need to put in your program to create a game like this?

At the very least, the game needs:

<ol>

 <li>a <strong>pokemon</strong>, with some number of hit points</li>

 <li>an <strong>action</strong> that our pokemon can perform</li>

</ol>

<p class="talk-box">This stuff is so common in computer games that you probably don’t even think of it. Using what you’ve learned so far about programming your discoboard, how might you keep track of your pokemon’s hit points (HP), and how could you represent an action in your program?

Given what you’ve covered so far, you probably decided to use a <strong>register</strong> to store the pokemon’s HP—good choice. Remember in last week’s lab you used registers and instructions to perform your <code>2</code>+<code>2</code> operation? This is the same idea. From the computer’s perspective the register is just a bunch of bits (well, a bunch of logic gates which store a bunch of bits) but as long as <em>you</em> know that that particular register really represents your pokemon’s HP and treat it accordingly, then your game will play just fine.

<p class="push-box">Write a program to store the pokemon’s HP in the register <code>r0</code> with an initial value of <code>100</code>. Commit your main.S file and push it up to GitLab.

You’re also hopefully by now starting to get the hang of the way things can go wrong in assembly programming. For example, if you try to write an instruction which branches to <em>itself</em>:

<pre><code class="language-arm hljs armasm"><span class="hljs-symbol">my_label</span>:  <span class="hljs-keyword">b </span>my_label</code></pre>

then your board will stop talking to you (that’s why we put a <code>nop</code> in the middle). And if you set a breakpoint on a label (like <code>my_label</code> above) then it may end up breaking one instruction <em>later</em> than you’d expect (this is a bug in the debugger). All of this stuff is a bit painful at first, but you will get the hang of it.

If all else fails, un-plug and re-plug your board (but once you get used to the pitfalls you shouldn’t have to do that too often).

<h2 id="condition-codes">Exercise 2: Healing your pokemon</h2>

Now that you’re storing the HP, here’s the first action in the game:

<ul>

 <li><strong>healing potion</strong>: add 50HP (hp := hp + 50)</li>

</ul>

Write an assembly instruction for this action. Remember to look at the <a class="acton-tabs-link-processed" href="https://cs.anu.edu.au/courses/comp2300/v_media/manuals/ARMv7-cheat-sheet.pdf">cheat sheet</a>—nobody expects you to recall this stuff perfectly from memory.

Step through your program—what’s the value in the hit point register after your “action” instruction has occurred? Does that seem right?

At this point, the Pokemon world only has one action, so the only way for the game to proceed is to keep performing that action. We can do that with a <strong>branch</strong> instruction: <code>b</code> (hint: check page 2 of your cheat sheet). This instruction tells your discoboard to “branch” (sometimes called a jump on other CPU types) to a different part of the code. You can specify the “destination” of the branch in a bunch of different ways, including using a label, or a constant value (if you know exactly what address you want to go to ahead of time) or even the address in a register. If you’ve wondered how to get your program to do something other than just keep following the instructions from top to bottom, branching is the answer.

Add a label and a branch instruction to modify your program so that the pokemon keeps drinking healing potions (one after the other) indefinitely.

Hit the continue (play) button in the debug toolbar and let the program run for a while, pausing every now and again to check the pokemon’s HP value—what do you notice?

<p class="talk-box">What do you think is going on here (think about this: what’s the biggest number a register can store?)

<h2 id="condition-codes">Exercise 3: Status Flags and Condition Codes</h2>

How can you deal with this problem? The answer lies is in the program status register in every ARMv7 CPU (including our little discoboard). You can see it in the <em>cortex registers</em> viewlet in VSCode under <code>xPSR</code>:

<img decoding="async" alt="Current program status register view in VSCode" data-recalc-dims="1" data-src="https://i0.wp.com/cs.anu.edu.au/courses/comp2300/assets/lectures/week-2/xpsr-viewlet.png?w=980&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

 <noscript>

  <img decoding="async" src="https://i0.wp.com/cs.anu.edu.au/courses/comp2300/assets/lectures/week-2/xpsr-viewlet.png?w=980&amp;ssl=1" alt="Current program status register view in VSCode" data-recalc-dims="1">

 </noscript>

Remember we talked about these status flags in the week 2 lectures (go and have a look if you need to refresh your memory). <a class="acton-tabs-link-processed" href="https://stackoverflow.com/a/24002847/1510867">This stackoverflow post</a> also has a nice “clock” animation to show how all the condition codes in the status register work.

When the discoboard executes any instruction with an <code>s</code> suffix (e.g. <code>adds</code>) it updates these status flags according to the result of the operation. That’s all the <code>s</code> does—<code>add</code> and <code>adds</code> will leave the exact same result in the destination register, but <code>adds</code> will update the flags to leave some “breadcrumbs” about the result (which can be helpful, as you’ll soon see).

In addition to this, if you look at the <em>Tests</em> section of the <a class="acton-tabs-link-processed" href="https://cs.anu.edu.au/courses/comp2300/v_media/manuals/ARMv7-cheat-sheet.pdf">cheat sheet</a> then you can see that there are some instructions specifically used to update the flags without changing the values in the general purpose registers (<code>r0</code> – <code>r12</code>). For example, <code>cmp r0, 10</code> is the same as <code>subs r0, 10</code> except that the value in r0 is left untouched.

<p class="info-box">Sometimes the status flags are called status bits, or condition flags, or condition codes, or some other combination of those words. They all refer to the same thing—the bits in the program status register.

It’s time to see this in action. Go back to your “healing loop” program from Exercise 1 and step through, but this time keep an eye on the <code>xPSR</code> register. As a tip, you probably want to bump up the “healing amount” to something like <code>0xF000000</code> so that it doesn’t take you a million steps before you overflow &#x1f642; What do you notice about the status register bits when the HP register switches to a negative (when viewed as a signed decimal number) value?

Write a series of simple programs (e.g. <code>mov</code> some values into registers, then do an arithmetic operation on those registers) to set (a) the negative flag bit (b) the zero flag bit (c) the carry flag bit and (d) the overflow flag bit.

Uncomment the “b exercise_3” instruction on line 7 of your project, and use the template underneath the exercise_3 label to fill out your solutions. When you’re done, you can re-comment “b exercise_3” and use it as a reference.

<pre><code class="language-arm hljs armasm"><span class="hljs-symbol">exercise_3</span>:<span class="hljs-comment">@ set the negative flag</span><span class="hljs-comment">@ ... your instruction(s) go here ...</span><span class="hljs-comment">@ set the zero flag</span><span class="hljs-comment">@ ... your instruction(s) go here ...</span><span class="hljs-comment">@ set the carry flag</span><span class="hljs-comment">@ ... your instruction(s) go here ...</span><span class="hljs-comment">@ set the overflow flag</span><span class="hljs-comment">@ ... your instruction(s) go here ...</span></code></pre>

<p class="info-box">If you’re getting bored of stepping through every instruction, don’t forget you can set breakpoints, these control exactly where your debugger will pause after clicking ‘continue’ (the green button). You can do this by clicking in the left-hand “gutter” (or margin) of the code view. You should see a little red dot appear:<img decoding="async" alt="A breakpoint (indicated by the red dot)" data-recalc-dims="1" data-src="https://i0.wp.com/cs.anu.edu.au/courses/comp2300/assets/labs/lab-1/VSCodium-breakpoint.png?w=980&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

 <noscript>

  <img decoding="async" src="https://i0.wp.com/cs.anu.edu.au/courses/comp2300/assets/labs/lab-1/VSCodium-breakpoint.png?w=980&amp;ssl=1" alt="A breakpoint (indicated by the red dot)" data-recalc-dims="1">

 </noscript>

<p class="push-box">Push up your program for Exercise 3.

<p class="extension-box">The program status register <code>xPSR</code> is a bit different from the other registers, and you can’t use it in all instructions. You can access it with special instructions, though—check out <code>MRS</code> and <code>MSR</code> in the <a class="acton-tabs-link-processed" href="https://cs.anu.edu.au/courses/comp2300/v_media/manuals/ARMv7-M-architecture-reference-manual.pdf">ARMv7 reference manual</a>. Can you think of a way (or an addition to your program) to make your life easier in reading the program status register?

It might seem like this carry/overflow stuff isn’t worth worrying about because it’ll never happen in real life. But that’s not true. It can cause serious problems, like literally <a class="acton-tabs-link-processed" href="https://www.bbc.com/future/story/20150505-the-numbers-that-lead-to-disaster">causing rockets to explode</a>. So understanding and checking the status flags really matters &#x1f642;

<h2 id="exercise-4-enriching-our-game-world">Exercise 4: Enriching our game world</h2>

At the moment our game is pretty boring, we have a pokemon that constantly increases it’s HP by drinking a healing potion. Let’s make the game more exciting by adding an enemy pokemon, that we can ruthlessly damage in each <em>turn</em>. This enemy pokemon will also need it’s own HP so that we can add a loss condition. This means that when the enemy pokemon eventually faints, we can tell (and win!)

Your pokemon will just have one attack for now, which subtracts a certain amount of health from our enemy pokemon.

You’ve seen in lectures (and every time you look at the cheat sheet) that most of the ARM assembly instructions can be made to execute “conditionally” using a one or two letter suffix. (e.g. <code>eq</code>, <code>mi</code>, <code>vs</code>)

<p class="think-box">What do these instruction suffixes do? What about the instruction <code>bne end_loop</code>. You can look at your cheat sheet if you like.

Go back up to the top of your program (just under the <code>main</code> label) and add the following instructions. Keep the rest of your pokemon code further down, just make sure this stuff is at the top.

<pre><code class="language-ARM hljs"><span class="hljs-keyword">movs </span><span class="hljs-built_in">r0</span>, <span class="hljs-number">#100</span><span class="hljs-keyword">beq </span>end_loop  <span class="hljs-comment">@ conditional branch 1</span><span class="hljs-keyword">subs </span><span class="hljs-built_in">r0</span>, <span class="hljs-number">#200</span><span class="hljs-keyword">beq </span>end_loop  <span class="hljs-comment">@ conditional branch 2</span><span class="hljs-keyword">adds </span><span class="hljs-built_in">r0</span>, <span class="hljs-number">#100</span><span class="hljs-keyword">beq </span>end_loop  <span class="hljs-comment">@ conditional branch 3</span></code></pre>

<p class="talk-box">Which of the <code>beq</code> instructions do you think will actually trigger the branch to the <code>end_loop</code>?

Then, step through and see what happens. Can you change the condition (i.e. change the <code>eq</code> to something else) to execute conditional branch 2? How about branch 1? Have a look at the “Condition codes” section of the cheat sheet to familiarise yourself with all the options.

Delete the above lines from your program and discuss how you can now use this “conditional branching” behaviour to make Pokemon work like a proper game:

<ol>

 <li>at the end of your pokemon’s turn, check if the enemy pokemon has fainted—if so, branch to a <code>pokemon_wins</code> loop at the end (you’ll have to add this in, too)</li>

 <li>otherwise if the enemy pokemon hasn’t fainted, branch back up to the start of your pokemon’s turn and start again.</li>

</ol>

<p class="push-box">Push up your program for Exercise 4 with two pokemon, each with their own HP and alternating turns. If the enemy pokemon’s health is 0 or less, branch to an infinite loop labelled <code>pokemon_wins</code>.

<h2 id="exercise-5-the-empire-fights-back">Exercise 5: the empire fights back</h2>

We’ve been a bit unfair to our enemy pokemon at this point–it has to just sit there and take damage! That’s not a very fun game, so lets give our enemy a turn attacking! Give your enemy pokemon it’s own turn and let it attack your pokemon back! Pick an attack that means that your pokemon faints just before the enemy pokemon would. This means we need another loss condition for our own pokemon, i.e. at the end of the enemy pokemon’s turn–if your pokemon has fainted, branch to an <code>enemy_wins</code> loop. If both of the pokemon are still awake at the end of both pokemon’s turns, branch back up to the start of your pokemon’s turn and start again.

Run through your code until your pokemon faints. Do all of the steps behave as expected?

<p class="push-box">Commit and push your enemy pokemon.

<h2 id="exercise-6-conditionals-and-branching">Exercise 6: conditionals and branching</h2>

We’re now going to do two things to make our game more interesting:

<ol>

 <li>Add in a “strength” variable for each of our pokemon (you’ll need to use two new registers for this, one for each pokemon). This strength variable will change the power of some of the attacks. Choose an initial value for the strength which makes sense to you (perhaps try 10 and see what happens)</li>

 <li>Add more attacks that do more than just decrease the health of our enemy</li>

</ol>

Here are some attacks we’ve come up with:

<ul>

 <li><strong>fireball</strong>: remove 20HP</li>

 <li><strong>frenzy</strong>: remove 30HP, but add 10 strength to the opposing pokemon</li>

 <li><strong>magic poison</strong>: do <em>double</em> the pokemon’s strength attribute in damage (hp := hp – 2 * strength)</li>

 <li><strong>strength from weariness</strong>: hp := hp + ((hp-strength)/20)^2</li>

 <li><strong>eye of Zardok</strong>: strength := (hp * strength) / 100; HP := HP – (strength / 16)^3</li>

</ul>

Note that these ones (unlike the simpler actions from the previous section) might require more than one instruction, so feel free to use whitespace and comments (any text on a line following the <code>@</code> character is a comment) to make things clearer. You can use whatever registers you like, but make sure that the <strong>destination</strong> register of each instruction isn’t already holding something important (like your HP!). For more complex mathematical expressions, you need to break it down into smaller steps. Use a pencil and paper if that helps.

Choose one of the first three, and one of the last two to implement yourself.

<p class="talk-box">Come up with an idea for your own action, share it with a neighbour (or send it to the rest of your lab on Teams).

Implement your own custom action (or someone else’s) and add it to either your pokemon or the enemy pokemon.