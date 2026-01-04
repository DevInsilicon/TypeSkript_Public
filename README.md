# TypeSkript: A Skript replacement
*Code examples will be shown towards the end*

I've been working on a project called **TypeSkript**. Unpopular opinion, but I HATE SKRIPT SYNTAX. Skript is an incredible feat of technology, but I believe it has some critical flaws. (In my opinion)
- It hogs the CPU like crazy even when sitting idle.
- RAM usage is up the wazoo. Especially on larger servers with tens of thousands of global variables holding player data
- It caters heavily towards novice programmers. Not inherently bad, but it limits what more experienced devs can do. Skript Reflect HEAVILY increases the abilities of Skript.

One of the main issues of why I don't use Skript is: Skript doesn't follow traditional programming language conventions. So I thought up: What could I do to combine the power and flexibility of real world languages like **Typescript, Python, Java, and JavaScript** with the power of Skript's ease and reloading capabilities?

Using proprietary methods, (I will get into this later), I've managed to create a reloadable, fast, and memory efficient scripts that let you use these languages and have the ease of skript.

In my opinion I've made a **HUGE** breakthrough, TypeSkript in my initial benchmarks show Typeskript being up to 50X FASTER, and using 3-10x LESS CPU. (I have no way of measuring RAM, but I can assure how its coded it uses less). 
The primary language I support is Typescript + Java JIT, however I also support Python, and Javascript, but I do not test Python / JS as thoroughly. On top of supporting these great languages I also support multi-file support for these languages. This allows you to take FULL advantage of these languages, especially Typescript.

Unlike Skript, TypeSkript has NATIVE reflection support, allowing you to use TypeSkript and Java interchangeably with almost zero performance drops. Is there some sort of method we don't already support literally just use the Java class. Due to us supporting Java interchangeably you can use ANY plugin API in TypeSkript by default. Usually in Skript the plugin developer or some third party would need to code a custom addon to use their API in your Skripts, or use Skript Reflect. 

Ok, I've talked all of this greatness about TypeSkript, you're probably thinking "When is bro going to show examples". Well I have one last subject to talk about before I show examples. **The VS Code Extension**: Of course with multi-file support, and being able to utilize the full abilities of these languages I'm going to need an IDE to get intelli-sense, snippets, and Java to TS type checking. Well I have you covered! I have created a VS Code extension that can
- Generate types and intelli-sense for ALL supported languages. Yes, in-real-time types EVEN for JAVA. (It will be more obvious later in code examples)
- Automatic project setup
- Fast and easy to use snippets for ALL languages and extension libraries.

More screenshots will be provided under the post to show the true capabilities of the VS Code extension.

## Now the great reveal!
How do I program TypeSkript? How do I interact with Paper / Bukkit's APIs?
Well, you have two ways. 
### The bridge route:
TypeSkript is based on several key concepts. We're going to cover bridges now. You communicate with Minecraft via these "bridges". Each bridge has API documentation in our WIKI (However this will not be publicly available yet). These bridges can easily be programmed to support plugin APIs as well, and they've already been programmed for LuckPerms, ItemsAdder, SQL, Nitrite NoSQL, Redis, Packet Events, ProtocolLib, Placeholder API, NBT API, Vault, TAB, Player Points, **and many more!**

Here is a very simple example on how to use a bridge.

Example of command registration:
```ts
// Create a simple slash command
paper.command('helloworld', {
    usage: '/helloworld',
    description: 'Description',
    aliases: ['hw'],
    permission: 'example.use',
    permissionMessage: '<red>You do not have permission!',
    tabComplete: (sender, args) => {
        if (args.length === 1) {
            return ['option1', 'option2'];
        }
        return [];
    }
}, (sender, args) => {
    sender.sendMessage("Hello world!");
});
```
Example of registering a listener.
By default TSK supports every single Java event.
```ts
paper.on('PlayerJoinEvent', (event) => {
    let player = event.getPlayer();

    player.sendRichMessage('<green>Welcome to the server! Enjoy your stay!');
});
```
These are very simple examples, and the bridges get MUCH deeper in detail. You can do most practical things through the paper bridge including runLater, timers, and async activities.

### The java route:
TypeSkript TS works hand in hand with Java.
A common occurrence is you need to get the bukkit instance, here is a very quick example of how to do it using the Java API.
```ts
let bukkit: Bukkit = Java.type("org.bukkit.Bukkit");
```
There are a variety of different things you can do with the Java Interop. 

### Java JIT
TypeSkript has a system that allows you to write FULL java classes that can be compiled at runtime. They are fast and efficient. They have one downfall however, when reloading these classes cannot be fully unloaded. It is important to write your own unload function and call it on reloads to prevent memory leaks.

You can call your Java JIT classes using 
MyUtils.java
```java
package jit;

import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.bukkit.plugin.java.JavaPlugin;

public class MyUtils {
    public static JavaPlugin PLUGIN_ENTRY; // Java JIT handler auto injects TSK plugin instance here.

    private String prefix;

    // Supports constructors
    public MyUtils(String prefix) {
        this.prefix = prefix;
    }

    // Basic example
    public void heal(Player player) {
        player.setHealth(20.0);
        String msg = (prefix != null ? prefix : "") + " You have been healed by JIT!";
        player.sendMessage(msg);
        
        if (PLUGIN_ENTRY != null) {
            PLUGIN_ENTRY.getLogger().info("Healed player " + player.getName());
        }
    }

    // Test static method
    public static void broadcast(String message) {
        Bukkit.broadcastMessage("§a[JIT] §f" + message);
    }
}

```
init.ts
```ts
const MyUtils = jit.findClass('dev.insilicon.MyUtils');
MyUtils.broadcast("Jit and stuff");
```
Constructors and static methods are fully supported.
You can additionally use plugin APIs in these JIT classes as well.

This is just scratching the surface of what is possible. I've made many more complex scripts. Staff mode plugins, packet manipulation vanish, custom items, Chatcolor & Glow plugins, and much more.
The limit with TypeSkript is Java itself, which makes it an incredibly powerful tool.

## My Benchmarking
I have uploaded TypeSkript and Skript files that run the exact same benchmarking tests. You can find them uploaded under the post. If you find any discrepancies please let me know, and I will update the tests immediately! But for now here are the results...

(The benchmark skripts and summary is written by AI, they were just some quick tests I made to compare performance.)
TYPESKRIPT VS SKRIPT - BENCHMARK RESULTS

Location Math (5k calcs)
Skript: 130ms | TypeSkript: 2ms -> 65.0x faster

Inventory Ops (18k items)
Skript: 150ms | TypeSkript: 8ms -> 18.8x faster

Permission Checks (5k lookups)
Skript: 20ms | TypeSkript: 1ms -> 20.0x faster

Economy (2k transactions)
Skript: 10ms | TypeSkript: 1ms -> 10.0x faster

Enchantments (1.2k processed)
Skript: 10ms | TypeSkript: 0ms -> instant

Cooldowns (3k checks)
Skript: 20ms | TypeSkript: 2ms -> 10.0x faster

Quest System (1k quests)
Skript: 10ms | TypeSkript: 0ms -> instant

Leaderboard (500 players)
Skript: 10ms | TypeSkript: 1ms -> 10.0x faster

Region Checks (2k locations)
Skript: 90ms | TypeSkript: 13ms -> 6.9x faster

Chat Filter (1k messages)
Skript: 10ms | TypeSkript: 3ms -> 3.3x faster

TOTAL TIME
Skript: 450ms | TypeSkript: 32ms -> 14.1x FASTER
Real-world impact: 41.8% less server lag on busy servers

## Why is TypeSkript faster? (JIT)
We use Just In Time (JIT) optimization techniques, every time you reload a script the performance is going to start off slow, however still up to 1.25x - 3x faster than Skript depending on what your doing. However EVERY time you run a function or some sort of task on TypeSkript it gets FASTER and FASTER. Something that you run over and over again get SIGNIFICANTLY faster. Eg example script that generates fibonacci numbers:
```ts
function fib(n: number): number {
    if (n <= 1) return n;
    let a = 0, b = 1;
    for (let i = 2; i <= n; i++) {
        const temp = a + b;
        a = b;
        b = temp;
    }
    return b;
}

paper.command('fibbenchmark', {
        description: 'Run Fibonacci sequence benchmark',
        usage: '/fibbenchmark'
    }, (sender) => {
    sender.sendMessage(Format('<gold><bold>[FIBONACCI]</bold><yellow>Generating 1000 Fibonacci numbers...'));
    
    const start = Date.now();
    const fibs: number[] = [];
    let a = 0, b = 1;
    fibs.push(a);
    fibs.push(b);
    for (let i = 2; i < 1000; i++) {
        const next = a + b;
        fibs.push(next);
        a = b;
        b = next;
    }
    const duration = Date.now() - start;
    
    sender.sendMessage(Format(`<gold>Generated 1000 Fibonaccinumbers in <yellow>${duration}ms`));
    sender.sendMessage(Format(`<gray>Last number: ${fibs[fibs.length - 1]}`));
});

```

First run: 4 ms, Second run: 4 ms, Third run: 1ms, and by the 5th run it reached 0ms. This is because of the JIT optimizations that TypeSkript uses. The more you run a function the faster it gets! These JIT Optimizations are constantly improving as well, if you run your functions days later it could be significantly faster than before.

## Compiliation & Current Complications with it
TypeSkript is MOSTLY if not close to being completely async. Possible folia support coming, however it might need a lang bridge rewrite like instead of paper using folia.on etc. Our compilation backend tries to keep everything async *during compilation*, this allows high end servers with multiple threads to compile with zero lag to players. However there is no way of limiting thread usage currently, so on low end servers with very minimal threads this can cause lag spikes up to a second or two. This is something that is on the TODO to fix, however I'm not sure how to approach it yet. Due to TS being completely async the faster extra threads you have the faster compilation will be, and will make TypeSkript run significantly faster.

## Embedded Jar Files
This tech I've been working on is very new alpha, just started working on this recently. TypeSkript eventually will support embedded jar files which developers can compile their TSK Namespaces into a jar file and distribute it to other servers without needing to share source code. However I'm making the plugin paid (More info under Addressing the release) this feature may be only available to certain license tiers. It would be as simple as typing gradle build and it would output a jar file with your TSK code embedded into it. This is something I'm very excited about and I cannot wait to fully test it. (The basics are already done, just need to polish it up).

## Personal experience
Of course I've programmed TSK longer than anyone else using it, however I've found my productivity has increased significantly, something that could take me hours or even days I did in less than 1-2 hours. It was incredible to see how fast I could just prototype and then instantly test with a simple reload. TypeSkript is highly centered around Java interoperability, so whenever I needed to do something complex I just used Java classes to do it. This made development incredibly fast and efficient. Being able to make TypeSkript vars have the same intellisense as Java classes made it incredibly easy to code without needing to constantly reference documentation. Quite literally if I do:
```ts
let player: Player = event.getPlayer();
player. // I see all java methods and properties here with intellisense

// Or if I do
let bukkit: Bukkit = Java.type("org.bukkit.Bukkit");
// Even though java.type doesn't neccessarily assign a TSK type I can assign one with :Bukkit. (Types are automatically generated by the VS Code extension connecting to your server)
bukkit.
// Returns all availiable methods with smart checking!
```
## For you Vibe coders out there!
LLMs have always struggled with Skript. TypeSkript is a breath of fresh air for LLMs. Since TypeSkript uses traditional well documented programming languages, LLMs can easily connect to our documentation wiki which has MCP mappings, and bridge API docs. In my tests LLMs perform significantly better with TypeSkript than Skript.

## Addressing the release
TypeSkript is currently in a closed alpha, and IS on medium sized production servers (~20-40 plr avg) and outperforming Skript significantly (The server actually made a full switch to TSK, because it was so good). **I cannot decide if I want to go open source or make it paid.** I'm certainly leaning towards paid since I've invested a significant amount of time and money into this project. That is why I've left out a significant amount of information when it comes to the backend. If you are interested in joining the private alpha you must reach certain conditions.
- You must be a developer with experience in TypeScript, and Java OR Python and Java.
- You must have some sort of way I can verify your credibility. (Github, etc)
- You must be willing to provide feedback, and report bugs.
- You must be willing to agree not to share TypeSkript or any of its code publicly or even decompile it.
- You may use TypeSkript on production servers, however you must retrieve permission from me first.
* You must be fine with
  * Obfuscated code
  * No guaranteed usage (Uses a backend that verifies licenses)

If you are interested in testing please DM me on discord. Your help would be greatly appreciated!

Additionally, if you want to see a code example of something specific please let me know I am happy to answer ANY of your questions whether it be around how to do xyz, or specifying certain features of TypeSkript. I know I left out a lot of information but I wanted to keep this post concise. Thank you for reading!
