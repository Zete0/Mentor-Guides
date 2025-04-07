# Experience
<sup><sup>(Author: Volshok)</sup></sup>
<br>
**<img src="ExperienceGain.gif">**
## Summary
Units accrue experience by inflicting damage on enemies. Unit experience provides percentage-based bonuses to specific attributes:

1.  Health Bonus: Increases the unit's maximum health pool. (Max: +250%)
2.  Reload Speed Bonus: Reduces the time between consecutive attacks. (Max: -55%)

The experience gained by a unit per attack is determined by the relative power between the target and attacker, as well as the proportion of the target's health lost during the attack.

-   Cost: Attacking stronger targets yields more experience.
-   Damage: Damage proportional to the target's health determines experience gained.

## Benefits
While all units gain bonus health and reload speed, some effects are unique to specific units.

### Repair Efficiency

The bonus health provided by experience indirectly improves repair efficiency. Since the cost of repair is proportional to the build power required, higher maximum health from experience allows faster health restoration for the same repair build-power used.

### Damage Output and Reload Speed

Most units exhibit increased damage per second (DPS) with faster reload speeds resulting from experience. However, weapons are capped at 10 shots per second. As such high fire rate weapons (like the Beamer anti-swarm laser) do not benefit from further reload speed reductions beyond this threshold. Additionally, some units do not gain rate of fire increases due to their unit script, like Whistlers & Lashers (unintended). Burst fire units also gain reduced (or no) bonus reload speed from veterancy: bursts cannot go any lower than 0.033 per shot since value lower automatically rounds to 0. 
As of now miniguns dps are misrepresented by veterancy bonus too, they do not gain any tangible bonuses apart from accuracy

### Unit-Specific Modifiers

Certain units have unique attribute enhancements tied to their experience level:

-   Gunslinger: Gains increased attack range in addition to standard health and reload speed bonuses.

### Veterancy  Levels 

There are 16 levels of veterancy, each with unique icons that display on both the unit card and above the units health bar, with the exception of rank 1, which has no icon.

| Icon | Rank | Title |
|--|--|--|
|<img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank1.png?raw=tru" alt="drawing" width="75"/>   | 1 |Drone |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank2.png?raw=tru" alt="drawing" width="75"/> | 2 |Initiate |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank3.png?raw=true" alt="drawing" width="75"/> | 3 |Seeker |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank4.png?raw=tru" alt="drawing" width="75"/> | 4 |Pathfinder |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank5.png?raw=tru" alt="drawing" width="75"/> | 5|Marksman |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank6.png?raw=tru" alt="drawing" width="75"/> | 6 |Breaker |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank7.png?raw=tru" alt="drawing" width="75"/> | 7 |Striker |
|<img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank8.png?raw=tru" alt="drawing" width="75"/>   | 8 |Guard |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank9.png?raw=tru" alt="drawing" width="75"/> | 9 |Warden |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank10.png?raw=true" alt="drawing" width="75"/> | 10 |Aegis |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank11.png?raw=tru" alt="drawing" width="75"/> | 11 | Pulse Captain |
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank12.png?raw=tru" alt="drawing" width="75"/> | 12 |Cobalt Enforcer|
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank13.png?raw=tru" alt="drawing" width="75"/> | 13 |Sentinel Major|
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank14.png?raw=tru" alt="drawing" width="75"/> | 14 |Assault Marshall|
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank15.png?raw=tru" alt="drawing" width="75"/> | 15 |Tungsten Prime | 
| <img src="https://github.com/beyond-all-reason/Beyond-All-Reason/blob/master/luaui/images/Ranks/rank16.png?raw=tru" alt="drawing" width="75"/> | 15 |Auric Sovereign | 





## Experience Calculation
Experience is calculated through the following formula:

```cpp
    if (!isCollision && baseDamage > 0.0f) {
    if ((attacker != nullptr) && !teamHandler.Ally(allyteam, attacker->allyteam)) {
    const float scaledExpMod = 0.1f * experienceMod * (power / attacker->power);
    const float scaledDamage = std::max(0.0f, (baseDamage + std::min(0.0f, health))) / maxHealth;
    attacker->AddExperience(scaledExpMod * scaledDamage);
    }
    }
```
### 1. Outer Condition Check:
  ```cpp
    if (!isCollision && baseDamage > 0.0f)  
```
 - `isCollision`: A flag to indicate if the event is a collision (ensures the logic only executes for non-collision events (e.g., damage caused by attacks or abilities).  
- `baseDamage > 0.0f`: Ensures that there is actual damage to process (ignores zero or negative damage values).

### 2. Target and Attacker Relationship Check:
```cpp
    if ((attacker != nullptr) && !teamHandler.Ally(allyteam, attacker->allyteam))
```
- `= nullptr`: Ensures that the attacker exists
- `!teamHandler.Ally(allyteam, attacker->allyteam)`:  Checks if the attacker and target are not allies (friendly fire is ignored).
   
### 3. Scaled Experience Calculation:

The experience gained by the attacker is scaled based on the following formula with two parts, `scaledExpMod` and `scaledDamage`:
```cpp
    const float scaledExpMod = 0.1f * experienceMod * (power / attacker->power);
```
####  Part 1: Calculate `scaledExpMod`:

-   `0.1f`: Constant balance multiplier for XP gain.
-   `experienceMod`: A mod that adjusts experience gain..
-   `power / attacker->power`: The ratio of the target's power to the attacker's power:
    
	-   If the target is more powerful, the ratio > 1, and the attacker gains more experience.
	-   If the attacker is stronger, the ratio < 1, leading to lower experience gain.
    

#### Part 2: Calculate `scaledDamage`:
```cpp
    const float scaledDamage = std::max(0.0f, (baseDamage + std::min(0.0f, health))) / maxHealth;
```
-   `baseDamage`: The damage value inflicted in this instance.
-   `health`: The target's current health after the attack
-   `std::min(0.0f, health)` ensures that only negative health values are considered (target health might go below zero).  
-   `std::max(0.0f, result)` ensures the scaled damage does not go below zero.
-   Division by `maxHealth`: Normalizes the damage relative to the target's maximum health, so experience scales proportionally to the damage dealt.  

### 4. Experience Gain for the Attacker:
```cpp
    attacker->AddExperience(scaledExpMod * scaledDamage);
```
-   `scaledExpMod`* `scaledDamage`: Combines the experience modifier and the normalized damage to determine the final experience gain.
-   `attacker->AddExperience()`: Adds the calculated experience to the attacker's experience pool.  
      
  

### Summary:

The code calculates experience for an attacker when dealing non-collision damage to a valid target (not an ally). The experience gain depends on:

1.  The relative power of the target and attacker.
    
2.  The proportion of the target's health lost during the attack.

Which means:

-   Attacking stronger targets yields more experience.
    
-   Damage proportional to the target's health determines experience gained
