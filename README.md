# backpack-packlister
 A backpack lootpool API for the GZDoom gameplay mod 'Hideous Destructor'.

# Features
Packlister allows mods implementing it's service class (or that include a PLINFO lump) access to the following features:
- whitelisting of any item by classname, 
- blacklisting of any item by classname,
- blacklisting of any item by the following types: pickup, mag, ammo, armor, weapon,
- blacklisting of any item by a minimum and maximum bulk value,
- customized blacklist and whitelist rules per container class,
- customized blacklist and whitelist rules for any container (global). 

# Syntax
Packlister command calls are written in the following format:
```
<A>.<B>: <C>, <D>, ... <N>;
//<E>
/*<F>*/
```
- A is a command's modifiers. Packlister supports the following command modifiers: "container",
- B is a command's name. Packlister supports the following command names: "whitelist, blacklist, minbulk, maxbulk, allowweapons, allowmags, allowammos, allowpickups, allowarmors",
- C is a command's first parameter. When using the modifier "container", <C> must be the name of the container you want to apply rules to,
- D is a command's second parameter. When using the modifier "container", <D> is the first parameter of the command you wish to invoke (IE, the first item to whitelist for whitelist, the bulkvalue for min/maxbulk).
- E is a single line comment. Comments are only supported for PLINFO lumps.
- F is a multiline comment. Comments are only supported for PLINFO lumps. 
- N is a command's Nth parameter. Packlister API commands can be given an arbitrary number of parameters, allowing for the whitelisting/blacklisting of multiple classes at once.
