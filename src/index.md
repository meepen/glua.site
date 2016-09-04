# DRAFTDRAFTDRAFTDRAFT

__Meepen's Guide to Garry's Mod's Lua API__  
*Because Lixquid triggered me*


Sections  
Section 0 - \`The Point\`
-  0.0 - \`What is this?\`
-  0.1 - \`Resources\`

Section 1 - \`Introductions\`
-  1.0 - \`Introduction to basic Garry's Mod Lua\`
-  1.1 - \`The hook library\`  




#### 0.0 - Introduction
-----------------------
In Garry's Mod, developers use an extended lua api with the LuaJit implementation of the language. The extended lua api contains interactive functions for Source Engine's internal code; if one is familiar with Source Engine's code they should be able to get into Garry's Mod Lua (GLua) fairly easily. This "book" aims to cover the fundamentals of Garry's Mod developing.

#### 0.1 - Resources
--------------------
Developers will need to interact with Source Engine in their code like drawing on the client's screen, creating console variables or even running something when a player writes in chat. For these types of things one will need to find a way to do it in Garry's Mod's lua api. The best place to find documentation for specific GLua functions is the Garry's Mod official wiki[1]. For stuff such as manipulating variables or function usage, look for a basic lua resource to get situated with the lua language, since this will not be covered here.


#### 1.0 - Introduction to GLua
-------------------------------

In GLua, developers will most likely be using hooks to run code. Hooks are one of the most essential part of Garry's Mod's API, as they can be used to do many things revolving around specific events. The usual hooks that will be used are going to be GAMEMODE hooks since they are a crucial part of the game experience in every gamemode. There will be many places where developers will need gamemode-specific hooks - such as sandbox's `CanTool` hook for suppressing specific tools for some user groups. 

However there are other ways to run code other than hooks; console commands, and file loads are also important places where code will run. Console commands will mostly be used for debugging and configs and file loading will be important to setting up the rest of the code.

When none of these situation can solve a problem, detouring comes into play. Detouring is a powerful, but should be the last resort for any situation.

#### 1.1 - The hook library
---------------------------
The hook library is one of the most powerful tools in Garry's Mod. You can debug with it, modify what happens, setup later code, or even reset your own code.

##### How hooks work (internally)
Internally hooks work pretty simplistically. Here's a mockup of the hook.Call function
```lua
function hook.Call(event_name, ...)
    for k,v in pairs(hook.GetTable()[event_name] or {}) do
        local r = v(...)
        if (r ~= nil) then
        return r
    end
    return (gmod.GetGamemode()[event_name] or no_operation)(...)
end
```

##### Adding hooks 
The function in the `hook` library that will be used most often is `hook.Add`. This function adds another function to be called when a specific event is fired. An example use-case is for logging when a player has died:
```lua
hook.Add("PlayerDeath", "print when a player dies", function(victim, inflictor, attacker)
    print(victim:Nick().." has died!")
end)
```
This example adds a function to be executed when `PlayerDeath` has been fired under the identifier `print when a player dies`. When the event has been fired it will print `$victimname has died!` to the console.

The return value in events are extremely important. If a hook returns any non-nil value it will stop the hooks that it hasn't run from running altogether. Only return a value when you are absolutely sure you want to do this.

##### Removing hooks
When a hook will not be needed any longer (such as unloading an addon), it should be removed with `hook.Remove`. It's fairly simple to use this - you use the first two arguments passed to `hook.Add` and pass them to `hook.Remove`.
```lua
hook.Remove("PlayerDeath", "print when a player dies")
```

##### Finding hooks
Finding hooks is essential to debugging, you can find why a hook won't run by looking at the source of the other hooks in the same event. Sometimes you may even use it to detour some of the hooks in an event to easily get your code to do something the way you want.



##### References
----------------

1. http://wiki.garrysmod.com