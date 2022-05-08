# Backpack Packlister
 A backpack lootpool API for the GZDoom gameplay mod 'Hideous Destructor'.

# Features
Packlister allows mods implementing it's service class (or that include a PLINFO lump) access to the following features:
- whitelisting of any item by classname, 
- blacklisting of any item by classname,
- blacklisting of any item by the following types: pickup, mag, ammo, armor, weapon,
- blacklisting of any item by a minimum and maximum bulk value,
- customized blacklist and whitelist rules per container class,
- customized blacklist and whitelist rules for any container (global),
- packlister features are mod agnostic (packlister is an optional dependency, due to it's serviceclass implementation). 

# Syntax
Packlister command calls are written in the following format:
```
<A>.<B>: <C>, <D>, ... <N>;
//<E>
/*<F>*/
```
- A is a command's modifiers. (before ".") Packlister supports the following command modifiers: "container",
- B is a command's name. (before ":") Packlister supports the following command names: "whitelist, blacklist, minbulk, maxbulk, allowweapons, allowmags, allowammos, allowpickups, allowarmors",
- C is a command's first parameter. When using the modifier "container", <C> must be the name of the container you want to apply rules to,
- D is a command's second parameter. When using the modifier "container", <D> is the first parameter of the command you wish to invoke (IE, the first item to whitelist for whitelist, the bulkvalue for min/maxbulk).
- E is a single line comment. Comments are only supported for PLINFO lumps.
- F is a multiline comment. Comments are only supported for PLINFO lumps. 
- N is a command's Nth parameter. Packlister API commands can be given an arbitrary number of parameters, allowing for the whitelisting/blacklisting of multiple classes at once.
- Multiple parameters are seperated by , and can be interupted by comments, newlines, or other characters,
- When sending multiple commands via a single string, ; is used to denote the end of one command call and the start of another (see above).

# Implementation
Packlister can be implemented staticallu, dynamically or via a mix of the two. 

## Static Implementation
Packlister can be utilized by including a PLINFO lump in your mod's root directory. This method is called 'static' since the contents of PLINFO are always loaded, regardless of your mod's settings.

## Dynamic Implementation
Packlister can be utilized by using a nonstatic eventhandler and searching for the service class "BackpackHandlerService". An example module is included below.
```
... ZSCRIPT LUMP ...



version "4.6"
class RemoverofItems : EventHandler
{
	bool hasticked;
	
	// Cached service pointer.
	service BackpackService;
	
	// Anything that is called on tick 0 is a valid option here.
	// WorldThingSpawned is chosen because by the time items populate the gameworld, eventhandlers are registered. 
	override void worldthingspawned(WorldEvent e)
	{
		if(!hasticked)
		{
		
			// Makes a service iterator and tells it to find the handler.
			ServiceIterator i = ServiceIterator.Find("BackpackHandlerService");
			
			// Cycles until the handler is found.
			while((BackpackService = i.next()) && !BackpackService){};
			
			// Only runs if the handler exists. 
			if(BackpackService)
			{
				int code = BackpackService.GetInt(
				"blacklist:
				hdshellammo,
				hdpistolammo,
				FourMilAmmo,
				HD4mMag,
				SevenMilAmmo");
			}
			hasticked = true;
		}
	}
}



... MAPINFO LUMP ...



GameInfo
{
	AddEventHandlers = "RemoverofItems"
}
```
Once located, "BackpackHandlerService"-'s function GetInt() should be called with a single string parameter. This parameter is treated as a chunk of a PLINFO lump, with the following differences:
- Single and Multiline Comments are not allowed to be sent directly to the service class. The reasoning behind this is single and multiline comments already are able to be implemented in .ZSC files, without a need to send the service class garbage information.  
This method is called 'dynamic' since due to being sent via a functioncall, commands can be dynamically generated and situationally sent (IE, tweaked/enabled/disabled alongside CVARS).

# Debugging
Packlister supports multiple debugging codes (similar to that of Hideous Destructor). When diagnosing an issue related to packlister, increment pl_debug by 1 until the information you are looking for is visible on mapload via the console. 