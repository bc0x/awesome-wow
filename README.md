# List of Awesome Classic WoW Resources

## Hunter Guide

---

### TODO

## Add-ons

---

[Reference](https://www.briankoponen.com/vanilla-world-warcraft-addons/)

1. [Risen UI](https://drive.google.com/file/d/0B5QT3H5F-mBXVFphQzd2WUxEa1E/view)
2. [Questie](https://legacy-wow.com/vanilla-addons/questie/)
3. [Informant](https://legacy-wow.com/vanilla-addons/informant/)

   - [EnhTooltip](https://legacy-wow.com/vanilla-addons/EnhTooltip/)
   - [Stubby](https://legacy-wow.com/vanilla-addons/stubby/)

4. [Super Macro](https://github.com/Monteo/SuperMacro)
5. [Zorlen](https://www.wowinterface.com/downloads/info4814-ZorlenFunctionsLibrary.html)
6. [Smart Restore](https://wow.curseforge.com/projects/project-2543)

7. [Equip Compare](https://legacy-wow.com/vanilla-addons/equipcompare/)
8. [YaHT](https://github.com/Aviana/YaHT)
9. [Aux](https://github.com/shirsig/aux-addon)
10. [Cartographer](http://www.vanilla-addons.com/dls/cartographer/) -- UNINSTALL
11. [OmniCC](http://www.vanilla-addons.com/dls/omnicc/) -- UNINSTALL

## Macros

---

1. ### Main Attack

Targets the nearest enemy and then attacks appropriately based on the distance
to the target.

```lua
/run if GetUnitName("target")==nil then TargetNearestEnemy() end
/run if CheckInteractDistance("target", 3) then RunMacro("Melee") else RunMacro("Range") end
```

Melee Macro

```lua
/run if(not isMonkActive()) then castMonkey();end
/run castAttack()
/run castRaptor()
/run castMong()
/run castClip()
```

Ranged Macro

```lua
/run if(not isMarked()) then castMark();end
/run if(not isHawkActive()) then castHawk();end
/run castAutoShot()
```

Pull / Kite

```lua
/script CastSpellByName(“Arcane Shot(Rank 1)”); SpellStopCasting();
```

2. ### Stings

`castSting()` will choose the appropriate sting for the target. It will also not
cast Serpent Sting if a mob is immune or is below 20% health to conserve mana.
That is why I have the modifier cast Serpent Sting directly. If I have to kite a
mob, I can force that sting.

```lua
/run --CastSpellByName("Serpent Sting")
/run if GetUnitName("target")==nil then TargetNearestEnemy() end
/run if IsShiftKeyDown() then CastSpellByName("Serpent Sting") else castSting() end
```

3. Wing Clip / Concussive Shot

This is a simple one that casts either Wing Clip or Concussive Shot depending on
distance. If the target is already Wing Clipped, it won’t cast Concussive Shot.

```lua
/run --CastSpellByName("Concussive Shot")
/run if CheckInteractDistance("target", 3) then castClip() else castCon() end
```

4. ### Pet Attack and Recall

| Pet Command | Situation                                                |
| ----------- | -------------------------------------------------------- |
| Attack      | Hunter’s target is an enemy.                             |
| Assist      | Hunter’s target is an ally.                              |
| Recall      | Hunter and Pet have same target or Hunter has no target. |

```lua
/script
if UnitExists("target") then
  if UnitIsFriend("player","target") then
    AssistUnit("target"); zFuriousHowl(); PetAttack(); zDash(); zDive();
  else if UnitExists("pettarget") and UnitIsUnit("target", "pettarget") then
    PetFollow(); zDash(); zDive();
  else
    zCowerAutocastOff(); zGrowlAutocastOn(); zFuriousHowl(); PetAttack(); zDash(); zDive();
  end;
end;
else PetFollow(); zDash(); zDive();
end;
```

5. ### Pet Feed / Mend / Revive / Call / Dismiss

The second macro handles all the other pet commands in one macro. This requires
SuperMacro’s Extended Lua function to work.

| Effect                    | Situation                                                                                                                                                               |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Call Pet                  | Pet isn’t out.                                                                                                                                                          |
| Revive Pet                | Pet is dead.                                                                                                                                                            |
| Mend Pet (Max Level)      | Pet is in combat and has target                                                                                                                                         |
| Mend Pet (Most Efficient) | Shift Modifier.                                                                                                                                                         |
| Feed Pet                  | Pet is out of combat and not happy. (Special Case: Pet is in combat without a target. Often after reviving, the pet is still marked as in combat even though it isn’t.) |
| Dismiss Pet               | Pet is happy and not in combat.                                                                                                                                         |

Feed Pet

```lua
/sr feed
```

PetCare

```lua
/run PetCare();
```

Extended Lua

```lua
function PetCare()
    if( IsShiftKeyDown()) then
      castMend();
    elseif (not UnitExists("pet")) then
      CastSpellByName("Call Pet");
    elseif (UnitHealth("pet")==0) then
      CastSpellByName("Revive Pet");
    elseif (Zorlen_petInCombat()) then
      if UnitExists("pettarget") then
        castMaxMend()
      else
        RunMacro("Feed Pet")
      end
    elseif (GetPetHappiness()~=3) then
      RunMacro("Feed Pet");
    else
      CastSpellByName("Dismiss Pet");
    end
end
```

6. ### Threat Management
   Hunter Increase Threat

```lua
/run --CastSpellByName("Distracting Shot")
/run castDistract();
/run zCowerAutocastOn();
/run zGrowlAutocastOff();
```

Pet Increase Threat

```lua
/run --CastSpellByName("Disengage")
/run zCowerAutocastOff();
/run zGrowlAutocastOn();
/run PetAttack();
/run castDisengage();
```

7. ### Traps

Explosive / Immolation

```lua
/run --CastSpellByName("Immolation Trap")
/run if (canTrap()) then if IsShiftKeyDown() then castExplosiveTrap() else castImmolationTrap() end else castFeign() end
```

Frost / Freezing

```lua
/run --CastSpellByName("Freezing Trap")
/run if UnitAffectingCombat("player") then castFeign(); PetFollow(); PetPassiveMode(); ClearTarget(); TargetLastEnemy(); else if IsShiftKeyDown() then castFrostTrap(); else CastSpellByName("Freezing Trap");end end
```

8. ### Aspects

This macro casts Aspect of the Monkey or Aspect of the Hawk based on the
distance to the target.

```lua
/run if CheckInteractDistance("target", 3) then castMonkey() else castHawk() end
```

9. ### Feign Death

This casts Feign Death only if the hunter is in combat.

```lua
/run --CastSpellByName("Feign Death")
/run if UnitAffectingCombat("player") then castFeign() end
```

10. ### General

Zorlen will save enough mana to cast Feign Death if you use its functions, so I
make a macro for basically every shot.

```lua
/run --CastSpellByName("Arcane Shot")
/run if GetUnitName("target")==nil then TargetNearestEnemy() end
/run castArcane()
```

This will only cast Hunter’s Mark if the target is not already marked.

```lua
/run --CastSpellByName("Hunter's Mark")
/run if(not isMarked()) then castMark();end
```

11. ### Healing

This uses the SmartRestore add-on to use a potion depending on need. Hold shift
to use a bandage.

```lua
/run if IsShiftKeyDown() then SR_Bandage(); else SR_SmartUse(); end
```
