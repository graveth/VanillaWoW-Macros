# VanillaWoW Macros
 
Most macros require [SuperMacro](https://github.com/Monteo/SuperMacro) addon, it adds some extra functions and allows writing more than 255 character macros.

Adding an SM_Extend.lua file to [SuperMacro](https://github.com/Monteo/SuperMacro) directory will allow for adding and usage of more functions.

## Generic

### FindAndUse Function
This function will search for an item in your bag and use it, you can add more than one item and will search and use the first one found in your bags.
```
function FindAndUse(...)
    for i,v in ipairs(arg) do
        local bag,slot=FindItem(v)
        if bag and slot then
            UseContainerItem(bag,slot)
            return true
        end
    end
    return false
end
```
Examples:
```
/run FindAndUse("Major Healthstone", "Whipper Root Tuber")
```
this macro will Use a Major Healthstone first then Use Whipper Root Tuber

```
/run if not FindAndUse("Major Healthstone") then CastSpellByName("Create Healthstone(Major)()") end
```
this macro will use a Major Healthstone if it is available and Conjure one if you don't have one in your bags

### Spammable Attack
This function will activate attack and it is spammable too, it require to have the attack spell in your action bar and changing the ID to the one of the bar
```
function StartAttack(Attk_ID)
    if not IsCurrentAction(Attk_ID) then
        UseAction(Attk_ID)
    end
end
```
Example:
```
/run StartAttack(12)
```
Note: you should have the attack spell in your 12 position of your action bar

### Spammable Stop Attack
This is the oposite of the above function
```
funtion StopAttack(Stop_ID)
    if IsCurrentAction(Stop_ID) then
        UseAction(Stop_ID)
    end
end
```
Example:
```
/run StopAttack(12)
```

### Get Spell ID by name
Some macro commands like `GetSpellCooldown` require the ID of spell you want to use, this is how you get the spell ID
```
function GetSpell_ID(spellname, spellrank)
    local i = 1;
    local name , rank = GetSpellName(i, BOOKTYPE_SPELL)
    while name do
            if name == spellname and rank == spellrank then
                return i
            elseif name == spellname and not rank == spellrank then
                local j = i +1
                local next_name, _ = GetSpellName(j, BOOKTYPE_SPELL)
                if next_name ~= name then
                    return i
                end
            end
            i = i + 1
            name, rank = GetSpellName(i, BOOKTYPE_SPELL)
    end
    return nil
end
```

## My Warlock Functions
Those Function are used by my warlock they are for Pet control all in one button without errors

### Pet Autocast Toggle
This function is used to toggle autocast of pet abilities, it is used in the other functions below
```
function PetSpellToggle(spellname)
    local i = 1;
    local name, _ = GetSpellName(i, BOOKTYPE_PET)
    while name do
        if name == spellname then
            ToggleSpellAutocast(i, BOOKTYPE_PET)
            return nil
        end
        i = i + 1;
        name, _ = GetSpellName(i, BOOKTYPE_PET)
    end
end
```

### Warlock Defensive Pet Abilities
If you have a Felhunter it will cast ```Devour Magic``` on the player<br />
If you have a Succubus it will toggle autocast for ```Lesser Invisibility```<br />
If you have a Voidwalker it will cast ```Sacrifice```<br />
If you have an Imp it will cast ```Fire Shield``` on the player<br />
```
function WarlockPetDef()
    if UnitCreatureFamily('pet') == "Felhunter" then
        CastSpellByName("Devour Magic", 1)
    elseif UnitCreatureFamily('pet') == "Succubus" then
        PetSpellToggle("Lesser Invisibility")
    elseif UnitCreatureFamily('pet') == "Voidwalker" then
        CastSpellByName("Sacrifice")
    elseif UnitCreatureFamily('pet') == "Imp" then
        CastSpellByName("Fire Shield", 1)
    end
end
```
In-Game add this macro
```
/run  WarlockPetDef()
```

### Warlock Offensive Pet Abilities
If you have a Felhunter:<br />
   &nbsp; If Control is pressed it will cast ```Devour Magic``` on the pet<br />
   &nbsp; If Shift is pressed it will cast ```Devour Magic``` on your target<br />
   &nbsp; else it will cast ```Spell Lock``` on your target<br />
If you have a Succubus:<br />
   &nbsp; If Control is pressed it will toggle on/off ```Lash of Pain```<br />
   &nbsp; If Shift is presser it will toggle on/off ```Seduction```<br />
   &nbsp; else it will cast ```Seduction```<br />
If you have a Voidwalker:<br />
   &nbsp; If Control is pressed it will cast ```Consume Shadows```<br />
   &nbsp; If Shift is pressed it will cast ```Suffering```<br />
   &nbsp; else it will toggle on/off ```Torment```<br />
If you have an Imp:<br />
   &nbsp; If your target is friendly it will cast ```Fire Shield```<br />
   &nbsp; else it will cast ```Firebolt```<br />
```
function WarlockPetOff()
    if UnitCreatureFamily('pet') == "Felhunter" then
        if IsControlKeyDown() then
            if UnitExists('target') then
                TargetUnit('pet') CastSpellByName("Devour Magic") TargetLastTarget()
            elseif not UnitExists('target') then
                TargetUnit('pet') CastSpellByName("Devour Magic") ClearTarget()
            end
        elseif IsShiftKeyDown() then
            CastSpellByName("Devour Magic")
        else
            CastSpellByName("Spell Lock")
        end
    elseif UnitCreatureFamily('pet') == "Succubus" then
        if IsControlKeyDown() then
            PetSpellToggle("Lash of Pain")
        elseif IsShiftKeyDown() then
            PetSpellToggle("Seduction")
        else
            CastSpellByName("Seduction")
        end
    elseif UnitCreatureFamily('pet') == "Voidwalker" then
        if IsControlKeyDown() then
            CastSpellByName("Consume Shadows")
        elseif IsShiftKeyDown() then
            CastSpellByName("Suffering")
        else
            PetSpellToggle("Torment")
        end
    elseif UnitCreatureFamily('pet') == "Imp" then
        if UnitIsFriend('player','target') then
            CastSpellByName("Fire Shield")
        else
            PetSpellToggle("Firebolt")
    end
end
In-Game add this macro
```
/run  WarlockPetOff()
```
