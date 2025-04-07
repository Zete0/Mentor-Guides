# Auto Heal
<sup><sup>(Author: Volshok)</sup></sup>
## Summary
All units in BAR begin repairing themselves one-minute after bezing damaged, generally at the rate of 5 HP/second. Some units (like the Commander) have additional healing that continuously repairs a set value. These two heals can stack, so a damage commander that hasn't taken damage for 1 minute will heal 10 hp/second.

## Types of Self-Repair
There are two kinds of self repairing mechanics for units in BAR.
1.  **Continuous Repair** (`AutoHeal`): A set amount of HP that the unit constantly repairs every second.
2.  **Delayed Repair** (`idleAutoHeal`): A set amount of HP that a unit repairs after not receiving damage for a set amount of time (`idleTime`). Idle time length is set by `idletime = 1800`, which at 1.00x game speed would be 1800/30 = 60 seconds
    
## Units with Unique autoHeal Values
The majority of units have `autoHeal=0`, meaning they do not heal while being attacked. Below are units that have have an `autoheal > 0`.
| Category | Name|  autoHeal  |
|:--:|:-:|:-:|
|Building|Armada Fortification Wall|12|
|Building|Armada Dragon's Teeth| 4 |
| Building|Armada Shark's Teeth | 4 |
| Building|Cortex Fortification Wall | 12 |
| Building|Cortex Dragon's Teeth| 4 |
| Building|Cortex Shark's Teeth | 4 |
| Bot|Armada Gunslinger | 50 |
| Bot| Armada Webber |15 |
| Bot|Armada Commander | 5 |
| Bot|Armada Butler | 5 |
| Bot|Cortex Commando | 9 |
| Bot|Cortex Commander | 5 |
| Vehicle|Cortex Printer | 5 |
| Ship|Armada Convey | 5 |
| Ship|Armada Ellysaw | 1.5 |
| Ship| Armada Skater |1.5 |
| Ship|Cortex Brimstone | 1.5 |
| Ship|Cortex Coffin |5 |
| Ship|Cortex Herring| 1.5 |
| Ship|Cortex Riptide | 1.5 |
| Submarine|Armada Eel | 2 |
| Submarine|Armada Grim Reaper | 2 |
| Submarine|Cortex Orca| 2 |
| Submarine|Cortex Death Calvary | 2 |

## Units with Unique Idle AutoHeal Values
The majority of units have `Idle AutoHeal=5`, below are units that have a different value.
| Category | Name|  idleAutoHeal  |
|:-:|:-:|:-:|
|Aircraft|Armada Falcon|10|
|Aircraft|Armada Highwind|10|
|Aircraft|Cortex Dragon|15|
|Aircraft|Cortex Valiant|10|
|Aircraft|Cortex Nighthawk|10|
|Building|Armada Dragon's Claw|10|
|Building|Armada Dragon's Fury|10|
|Building|Cortex Bulwark|2|
|Building|Cortex Devastator|2|
|Building|Cortex Dragon's Maw|10|
|Building|Cortex Heavy Mine|10|
|Experimental|Armada Titan|25|
|Experimental|Cortex Behemoth|40|
|Experimental|Cortex Shiva|15|
|Mine|Armada Heavy Mine|10|
|Mine|Armada Light Mine|10|
|Mine|Armada Medium Mine|10|
|Mine|Cortex Heavy Mine|10|
|Mine|Armada Light Mine|10|
|Mine|Armada Medium Mine|10|
|Bot|Cortex Commando|9
|Seaplane|Armada Cyclone|10|
|Seaplane|Cortex Bat|10|
|Ship|Armada Epoch|25|
|Ship|Armada Haven|15|
|Ship|Armada Ellysaw|2|
|Ship|Armada Maelstrom|2|
|Ship|Cortex Black Hydra|25|
|Ship|Cortex Oasis|15|
|Ship|Cortex Brimstone|2|
|Ship|Cortex Riptide|2|
|Submarine|Armada Barracuda|10|
|Submarine|Armada Eel|8|
|Submarine|Armada Grim Reaper|3|
|Submarine|Armada Serpent|15|
|Submarine|Cortex Death Cavalry|3|
|Submarine|Cortex Kraken|15|
|Submarine|Cortex Orca|8|
|Submarine|Cortex Predator|10|


## Auto Heal Calculation
Self-repairing is calculated through the following formula:

```cpp
    if (health < maxHealth) { 
	    health += (unitDef->idleAutoHeal * (restTime > unitDef->idleTime)); 
	    health += unitDef->autoHeal; 
	    health = std::min(health, maxHealth); 
    } 
```
### Code Overview

The code checks if a unit's **current health** (`health`) is less than its **maximum health** (`maxHealth`). If true, the unit's health is healed incrementally using two different healing factors:
 - **Continuous Repair**: A smaller constant healing value that applies regardless of the idle state.
 - **Delayed Repair**: Conditional healing that applies only if the unit has been idle for a specified time.

Finally, the health is capped at the unit's maximum health.
```cpp
	autoHeal     = udTable.GetFloat("autoHeal",      0.0f) * (UNIT_SLOWUPDATE_RATE * INV_GAME_SPEED);
	idleAutoHeal = udTable.GetFloat("idleAutoHeal", 10.0f) * (UNIT_SLOWUPDATE_RATE * INV_GAME_SPEED);
	idleTime     = udTable.GetInt("idleTime", 600);
```
### Notes
The engine multiplies the autoheal by `15 * 1/30` as part of `autoHeal     = udTable.GetFloat("autoHeal",      0.0f) * (UNIT_SLOWUPDATE_RATE * INV_GAME_SPEED);`

This means in json export dumped by /exportdefs, the listed value will be half of the true value.
