---
id: skill-gmod-addon-maker
type: skill
name: gmod-addon-maker
description: 'A tool for creating and managing Garry''s Mod addons, including Lua
  scripting, content creation, and addon packaging.

  Use when: developing new addons, writing Lua scripts for GMod, organizing addon
  files, or when user mentions Garry''s Mod, GMod, Lua scripting, or addon development.

  '
category: gmod-addon-maker
complexity: medium
keywords:
- api
- performance
- test
- testing
capabilities: []
token_estimate: 366
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~366 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# gmod-addon-maker

> A tool for creating and managing Garry's Mod addons, including Lua scripting, content creation, and addon packaging.
Use when: developing new addons, writing Lua scripts for GMod, organizing addon files, or when user mentions Garry's Mod, GMod, Lua scripting, or addon development.


# GMod Addon Maker
You are a GMod addon development assistant, skilled in Lua scripting, content creation, and addon packaging for Garry's Mod.

## When to Apply
Use this skill when:
- Developing new addons for Garry's Mod
- Writing Lua scripts for GMod
- Debugging GMod addons
- Organizing addon files and directories
- Packaging addons for distribution

## Addon Development Workflow
When creating a GMod addon, follow these steps:
1. **Conceptualization**
   - Define the addon’s purpose and features.
   - Identify target audience and use cases.
2. **Lua Scripting**
    - **Structure**: Follow the file organization patterns defined in [addon-structure](references/addon-structure.md).
    - **Core Concepts**: Use [gmod-lua-states](references/state-exp.md) to understand strictly defined Server/Client/Shared realms.
    - **Specific API Lookup Rule**:
        - **STRICT PROHIBITION**: You are **FORBIDDEN** from constructing URLs by guessing (e.g., Do NOT try `wiki.facepunch.com/gmod/hook`). Most guessed URLs are 404 errors.
        - **Action Sequence**:
            1. **Search Query**: If you have a search tool, use query `"gmod wiki <term>"` first to extract the correct URL.
            2. **Navigation**: If you must browse manually, you just fetch url and search the content,the url is `https://wiki.facepunch.com/gmod` and the search term is the API or concept you want to find. Do NOT guess URLs.
            3. **Read & Follow**: Read the index page content to find the specific function link.
3. **Content Creation**
    - Create or source models, textures, sounds, and other assets as needed for the addon.
    - Ensure all content is properly licensed for use in your addon.
    - Ensure content is optimized for performance and compatibility.
4. **Testing and Debugging**
    - Tell user to test the addon in-game to identify and fix bugs or issues.
    - See the [common-issues](references/common-error.md) reference for common problems and solutions during addon development.
    

---


## 📚 Reference Materials


### Common Error

#  Common Errors 

##  Attempt to call global '?' a nil value 

**Description:** You tried to call a function that doesn't exist.

**Possible causes:** 
* Your function might be defined in another Lua state. (e.g Calling a function on the client that only exists on the * server.)
* You're using a metafunction on the wrong kind of object. (e.g. Calling :SteamID() on a Vector)
* The function you're calling has an error in it which means it is not defined.
* You've misspelled the name of the function.

**Ways to fix:**
* Make sure the function exists
* Make sure your function is defined in the correct realm
* Check your function calls for spelling errors


##  Attempt to perform arithmetic on global '?' (a nil value) 

**Description:** You tried to perform arithmetic (+, -, *, /) on a global variable that is not defined.

**Possible causes:** 
* You tried to use a local variable that was defined later in the code
* You've misspelled the name of the global variable

**Ways to fix:**
* Make sure you define local variables before calling them in the code
* Check for spelling errors


##  Attempt to perform arithmetic on '?' (a type value) 

**Description:** You tried to perform arithmetic (+, -, *, /) on a variable that cannot perform arithmetic. (e.g. 2 + "some string")


##  Attempt to index global 'varname' (a nil value) 

**Description:** You tried to index an undefined variable (e.g. `print( variable.index )` where `variable` is undefined)

**Possible causes:**
* The variable is defined in a different realm
* The variable is local and defined later in the code
* You've misspelled the name of the variable

**Ways to fix:**
* Make sure the variable is only accessed in the realm it was defined in
* If the variable is local, define it before accessing it


##  Malformed number near 'number' 

**Description:** There is a malformed number in the code (e.g. 1.2.3, 2f) 

**Possible causes:**
* An IP address was written as a number instead of a string
* Incorrect writing of multiplication of a number and a variable
* Trying to concatenate a number to a string without a space between the number and the operator.

**Ways to fix:**
* Store IP addresses as a string
* Multiply variables with numbers by using the ***** operator
* Put a space between the concat (**..**) operator and the number.


##  Unexpected symbol near 'symbol' 

**Description:** You typed a symbol in the code that Lua didn't know how to interpret.

**Possible causes:**
* Incorrect syntax (e.g. Forgot to write "then" after an if statement)
* Not closing brackets and parentheses at the correct locations

**Ways to fix:**
* Make sure there are no mistypes in the code
* Close brackets and parentheses correctly (See: Code Indentation)


##  'symbol1' expected near 'symbol2' 
**Description:** Lua expected symbol1 instead of symbol2.
When 'symbol2' is <eof>, Lua expected a symbol before the end of the file

**Possible causes:**
* Not closing all brackets, parentheses or functions before the end of the file
* Having too many `end` statements
* Wrong operator calling (e.g. "==" instead of "=")
* Missing comma after table item.

**Ways to fix:**
* Close brackets and parentheses correctly (See: Code Indentation)
* Use the correct operators
* Add a comma after a table item

## Couldn't include file 'file' - File not found (<nowhere>)
**Description:** The file system tried to include a file that either doesn't exist or was added while the server was live.
This error can also be a `AddCSLuaFile` error.

**Possible causes:**
* Attempting to include / AddCSLuaFile a file that doesn't exist or is empty
* Creating a file while the server is still live

**Ways to fix:**
* Add the non-existent file, make sure the file isn't empty
* Restart the server



### State Exp

<title>States / Realms</title>

Lua states, also known as realms in Garry's Mod, are separate instances of the Lua language that can only interact and communicate with one another through indirect means such as the `net` and `file` libraries. As they are separate Lua states, global variables in one state cannot be retrieved in another without using indirect communication methods.

States load their custom functions and callbacks through Lua files included by the engine. All other files except those explicitly listed under each state below must be <page text="included">Global.include</page> from Lua to be used.

There are 3 Lua states in Garry's Mod:

# Client
<upload src="19952/8d7b58bc25e14dd.png" size="342" name="image.png" />
The **client** state is the Lua state representing the game client. It takes player input and sends it to the server while receiving data about other entities and players, then uses all of this information for <page>Prediction</page>. The client simulates entities in sync with the server's tickrate, but will perform <page text="rendering">Render Order</page> every frame.

The **client** state can interact and communicate with **server** state via the <page>net</page> library and <page text="running serverside concommands">Global.RunConsoleCommand</page>.

Lua code can detect if it is running in the **client** state by checking if the `CLIENT` global is `true`.


**Engine Lua Files - Client**
* `lua/includes/init.lua`
* `lua/derma/init.lua`
* `lua/gamemodes/base/gamemode/cl_init.lua` (this is always loaded regardless of the set gamemode)
* `lua/autorun/*.lua`
* `lua/autorun/client/*.lua`
* `lua/postprocess/*.lua`
* `lua/vgui/*.lua`
* `lua/matproxy/*.lua`
* `lua/skins/default.lua`
* `lua/gamemodes/<gamemode_name>/gamemode/cl_init.lua`
* `lua/gamemodes/base/entities/entities/*.lua`
* `lua/gamemodes/base/entities/entities/*/cl_init.lua`
* `lua/gamemodes/base/entities/weapons/*.lua`
* `lua/gamemodes/base/entities/weapons/*/cl_init.lua`
* `lua/entities/*.lua`
* `lua/entities/*/cl_init.lua`
* `lua/weapons/*.lua`
* `lua/weapons/*/cl_init.lua`
* `lua/gamemodes/<gamemode_name>/entities/entities/*.lua`
* `lua/gamemodes/<gamemode_name>/entities/entities/*/cl_init.lua`
* `lua/gamemodes/<gamemode_name>/entities/weapons/*.lua`
* `lua/gamemodes/<gamemode_name>/entities/weapons/*/cl_init.lua`

# Server
<upload src="19952/8d7b58d7428c9c6.png" size="337" name="image.png" />
The **server** state is the Lua state representing the game server. It can either be on the same system as the game client through a Listen Server, or on a separate system as a Dedicated Server. It takes input from players, performs physics and entity simulation at a static tickrate, then networks the result to all connected players.

The **server** state can interact and communicate with the **client** state via the <page>net</page> library (and formerly via the <page>umsg</page> library).

Lua code can detect if it is running in the **server** state by checking if the `SERVER` global is `true`.


**Engine Lua Files - Server**
* `lua/includes/init.lua`
* `lua/gamemodes/base/gamemode/init.lua` (this is always loaded regardless of the set gamemode)
* `lua/autorun/*.lua`
* `lua/autorun/server/*.lua`
* `lua/gamemodes/<gamemode_name>/gamemode/init.lua`
* `lua/gamemodes/base/entities/entities/*.lua`
* `lua/gamemodes/base/entities/entities/*/init.lua`
* `lua/gamemodes/base/entities/weapons/*.lua`
* `lua/gamemodes/base/entities/weapons/*/init.lua`
* `lua/entities/*.lua`
* `lua/entities/*/init.lua`
* `lua/weapons/*.lua`
* `lua/weapons/*/init.lua`
* `lua/gamemodes/<gamemode_name>/entities/entities/*.lua`
* `lua/gamemodes/<gamemode_name>/entities/entities/*/init.lua`
* `lua/gamemodes/<gamemode_name>/entities/weapons/*.lua`
* `lua/gamemodes/<gamemode_name>/entities/weapons/*/init.lua`

# Menu
<upload src="19952/8d7b58bce4f33d1.png" size="343" name="image.png" />
The **menu** state is an isolated internal Lua state on the game client, used solely for the main menu. It has extra functions missing from the **client** state. Menu modifications and addons are not officially supported, thus running **menu** state code requires overriding one of the engine's Lua files. For this reason, you won't need to worry about making your code work on the **menu**, unless you're deliberately overwriting something.

The **menu** state cannot interact with the **client** or **server** states; that is, the **menu** state cannot run any function that will make a callback occur on the **client** or **server**. The **client** and **menu** states can indirectly communicate with each other through the <page>file</page> library as they always share a common filesystem.

Lua code can detect if it is running in the **menu** state by checking if the `MENU_DLL` global is `true`.


**Engine Lua Files**
* `lua/includes/init_menu.lua`
* `lua/derma/init.lua`
* `lua/menu/menu.lua`
* `lua/vgui/*.lua`

# Other

These are not actual states; rather, they signify that the function or hook they represent can be executed in either one of their specified states:

- Shared (Client and Server)
<upload src="19952/8d7b58d999caa0e.png" size="487" name="image.png" />
- Client and Menu
<upload src="19952/8d7b58a905836e9.png" size="506" name="image.png" />
- Shared and Menu (all states - Client, Server and Menu)
<upload src="19952/8d7b58e58180414.png" size="552" name="image.png" />

These don't necessarily mean the function/hook will return the same values in the different states, or that it does the same thing. For example, the function <page>Entity:GetPos</page> can be called on either Server or Client, so it is considered "shared." <page>undo.GetTable</page> is also a shared function, but it works differently on Client and Server, as explained in its description. On the other hand, libraries like <page>math</page> and <page>string</page> have identical functionality in all 3 states.

# "Shared" isn't a state

It's important to remember that the **Server** and **Client** states (known colloquially as "Shared") are completely separate at runtime, and they cannot coexist. Meaning, something like this:

```lua
if SERVER and CLIENT then   -- always false...
end
```

makes no sense, because the condition `SERVER and CLIENT` will always return `false` due to both states' mutual exclusivity. Only one of the two can be true at any given point.

So, don't be fooled by the "Shared state"; it's not really a state at all. "Shared" does not mean that `CLIENT` and `SERVER` will both be `true` at the same time - it's just a shorthand way to communicate that a piece of code can be run on *either* the Server or Client at any point, but not both "at once."



### Addon Structure

# Lua Folder Structure
The main game directory for Garry's Mod is the `garrysmod` folder located next to the game's executable `hl2.exe`, and contains much of the content you see while playing the game.  This includes Lua scripts, sound effects, 3D models, and more.  Each Addon for Garry's Mod needs its own folder in `garrysmod/addons/` that will hold all of its content.

Addons and Gamemodes have a nearly identical folder structure to the `garrysmod` folder.  When making an Addon, all folders are optional and only those that your Addon requires should be created in order to keep your Addon's folder structure clean and easy to navigate.

Because this folder structure is applicable in multiple situations, the generic term `{Root}` is used here to mean the folder at the base of the game, Addon, or Gamemode.
Folders that are meant primarily for use by Garry's Mod internally are marked with `[I]` to mean "Internal" and should not be modified unless you have a good reason to be doing so.  Keep in mind that adding or modifying files outside of an Addon or Gamemode is discouraged because it can lead to file conflicts and unexpected behaviors.  With very few exceptions, any change or addition made to Garry's Mod should be done using an Addon or a Gamemode.

**Please note:** This is **not** an exhaustive list.  This list is primarily intended to give a general overview of the game's folder structure as it relates to lua scripts, with more detailed information where it is relevant for Gamemode and Addon creation.

` {Root} `  
`   ├── lua`  
`   │    ├── autorun` - Scripts in this folder are run automatically when Lua starts on both [Client](https://wiki.facepunch.com/gmod/States#client) and [Server](https://wiki.facepunch.com/gmod/States#server) (Called ["Shared"](https://wiki.facepunch.com/gmod/States#sharedisntastate)) realms.   
`   │    │    ├── client` - Runs its contents automatically in the [Client](https://wiki.facepunch.com/gmod/States#client) realm.  
`   │    │    ├── server` - Runs its contents automatically in the [Server](https://wiki.facepunch.com/gmod/States#server) realm.  
`   │    │    └── properties` - Holds the default <page text="Properties">properties</page> used in <page>The Context Menu</page>, which are loaded by `lua/autorun/properties.lua`  
`   │    ├── bin` - Where <page text="Binary Modules">Creating_Binary_Modules</page>, an advanced feature, should be placed.  
`   │    ├── derma` - Where <page>Derma</page> utility scripts should go.  
`   │    ├── drive` - A dedicated folder for scripts relating to the <page>Drive</page> system.   
`   │    ├── effects` - Where scripts for custom <page text="Lua-based Effects">util.Effect</page> go.  
`   │    ├── entities` - Scripts placed here will be loaded as <page text="Scripted Entities (SENTs)">Scripted_Entities</page>.  
`   │    ├── includes` - `[I]` This is where `init.lua` (The first lua file executed when lua starts) is located.  
`   │    ├── matproxy` - For scripts relating the <page text="Material Proxy (matprox)">matproxy</page> library, used to create per-entity materials.  
`   │    ├── menu` - `[I]` Similar to the `includes` directory but for the game's main menu.  Starts by running `menu.lua`.   
`   │    ├── postprocess` - Where scripts defining <page>Post-Processing Materials</page> are located.  
`   │    ├── skins` - Where scripts defining <page text="Skins">Derma_Skin_Creation</page> for the <page>Derma</page> library are placed.   
`   │    ├── vgui` - Scripts defining <page>Derma</page> interface elements like <page text="DButtons">DButton</page> and <page text="DSliders">DSlider</page>.  
`   │    └── weapons` - Scripts placed here will be loaded as <page text="Scripted Weapons (SWEPs)">Structures/SWEP</page>  
`   │         └── gmod_tool` - This folder is created by the Toolgun in the <page text="Sandbox">gamemodes/Sandbox</page> Gamemode.  
`   │             └── stools` - This is where lua files for <page text="Scripted Tools (STOOLs)">Structures/TOOL</page> used with the Toolgun are placed.   
`   └── gamemodes` - <page text="Gamemodes">Gamemode_Creation</page> can be thought of as Addons that are only active while they are being played.  
`        └── {Gamemode Name}` - Each Gamemode gets its own folder, which acts as its `{Root}` directory.  
`             └── gamemode` - This folder **must** exist and **must** contain at least `init.lua` and `cl_init.lua` for the Gamemode to work.



---

## 🚀 Usage

**Reference this template:** `@skill-gmod-addon-maker.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
