# Customizing AI chatter

Hi there, thanks for your interest! Let's de-mystify how the AI chatter in Konkr works!

Aside of satisfying your curiosity, feel free to use this secret knowledge to:
- Customize the AI chatter to your liking - you can create your own chatter file (You can use the [default](./default.md) one as template or start with something much more simple),
drop it into the game window on [https://www.konkr.io](www.konkr.io) and the game will import it! 
- Help me improve the default chatter file. Whether you spotted some grammar error or just have a cool idea for more lines the AI could use in a particular situation to add variety, all contributions are welcome!
- Help me localize the AI chatter to your language!
- Help me create an alternative AI chatter style - Noble and polite? Sarcastic and snarky? You name it!

The ideal way to submit a customized AI chatter for review and possible addition to the game is to [fork this repository](https://github.com/michal-bures/konkr_data/fork) and create a pull request.

But if you're not that familiar with GitHub, you can also just post your chatter file to the [discord server](https://discord.com/invite/C9HucB9arH) or [mail it to me](mailto:dev@konkr.io).

Now let's dig into it!

## How does the chatter file work?

It's really quite simple! AI chatter file is just a glorified [Markdown](https://en.wikipedia.org/wiki/Markdown) file with a list of simple **rules**. Each rule tells the game what the AI
rivals should say in a particular situation.

* For example, here's the <a href="./default.md" target="_blank">[default chatter file](./default.md)</a> used by the game. 
* And here it is in <a href="https://raw.githubusercontent.com/michal-bures/konkr_data/master/ai-chatter/default.md" target="_blank">plain text without formatting</a> for easy download or copy/paste

### So what exactly is a rule?

Here's an example of a rule:

```
> suffered damage, not hostile, common enemy 
- We should stop this fight, we have a bigger problem brewing.
- Our common foe smiles as we fight. We should stop this.
- You sure you want to keep fighting ME?
- You sure I'm still your biggest problem?
```

The first line, starting with `>`, is a list of **tags**, which tell the game when to use this rule. In this case, the
rule will only be used when:

* the AI faction took some losses caused by the player this turn (`suffered damage`)
* is not openly hostile towards the player (`not hostile`)
* there is a dominant faction on the map which is hostile towards both the player and the AI faction (`common enemy`)

For an exhaustive list of available tags, see [Available Tags](#available-tags) below.

Finally, the lines starting with `-` specify what the AI rival may say. When the rule is triggered, one of these lines
is picked at random.

### Rule priority

It's important to know that the game always uses **the first matching rule that it finds** (going from top to bottom)
when deciding what the AI rival will say. "Matching" means that **all** the tags in the rule are applicable to the
current situation.

So for example, if you had these two rules:

```
> fighting, hostile
- Less talk, more fight.
- I'm not letting you win this.
- This is it, it's you or me now.

> fighting, hostile, was stronger, weaker
- Gah, I'm in trouble now.
- Oh crap, I guess I missed that.
- Oh no! How did this happen!
- Oh no, I'm in trouble now, aren't I. 
```

The second one would never get picked! The first one matches in all the same situations, and it's higher in the list.

So as a rule of thumb, rules that are more important than others and/or more specific than others belong higher in the
list.

### Organizing your chatter file

Since any lines not starting with `>` or `-` are ignored by the rules parser, you can use them to
add comments and give your chatter file some structure. This is very advisable, since otherwise you will likely get
lost in the sea of rules.

The chatter file format is deliberatly made to play well with the [Markdown](https://en.wikipedia.org/wiki/Markdown)
syntax,
so it can be convenient to edit it with a markdown-friendly editor and use headings and other Markdown formatting to organize your chatter file.

### The default chatter file

For an example of how the chatter file can be organized, let's look at [the default chatter file](./default.md).

It uses headings to structure the file into the following sections (note that this list might get a bit outdated as I
keep iterating on the default chatter file):

1. Surrender
    - This is at the top because there's a standalone list of rules specifically for when the rivals are surrendering,
      and these should take precedence before all other rules.
2. When furious, simply throw insults
    - A list of rules for when the AI is very hostile to the player. This is so high in the list, because when the rival
      is furious, he should just vent his anger and not really make any of the more nuanced comments described below.
3. React to unprovoked player attacks
    - If the player just broke peace by attacking the rival, that definitely deserves a fitting reaction.
4. Explain your own unprovoked attacks
    - If it's the AI ruler who just broke peace by attacking the player, they should comment on the main reason why they
      did it.
      This is probably the most important set of rules in the file, teaching the player more about how the AI choses who
      to attack.
5. Comment on the ongoing fight
    - If none of the above applies and there is an ongoing fight between the AI and the player (both sides are attacking
      each other), choose a fitting comment based on how the war is going.
6. Comment on player dominance
    - If the player is clearly dominating the map, let the rival make it apparent that they are bothered by this.
7. Comment on rivalry
    - If the game turned into practically a 1v1 battle for victory at this point, make a fitting comment.
8. Comment on common enemy
    - If there's a third faction much stronger than both the rival and the player, comment on that situation.
9. Idle comments
    - Finally, if none of the above applies, pick an idle comment based on the power and relation dynamic between the
      rival and the player.

As you can see, there is a very deliberate intent behind how these sets of rules are ordered in the file.

## Available Tags

Here's a catalogue of all the tags that can be used in the rules, grouped by category.

### War and peace

Are the player and the AI rival currently fighting each other?

| Tag         | Meaning                                                                                                             |
|-------------|---------------------------------------------------------------------------------------------------------------------|
| `truce`     | The rival didn't attack player last turn and the player also didn't attack the rival this turn.                     |
| `invaded`   | Player just attacked the rival, while the rival did NOT attack the player last turn.                                |
| `invading`  | The rival just attacked the player and the player did NOT attack the rival during this and the previous turn.       |
| `fighting` | The rival attacked the player on their turn and the player also attacked the rival on this or the previous turn. |                                                        

These tags are mutually exclusive - precisely one of them will be active in any given situation.

### Damage caused to the player

During the rivals last turn, how much damage did they inflict to the player?

| Tag                   | Damaged inflicted to the player                        |
|-----------------------|--------------------------------------------------------|
| `caused no damage`    | None.                                                  |
| `caused minor damage` | Insignificant, like a tile or two.                     |
| `caused major damage` | Significant damage that noticably weakened the player. | 

Additional tags that overlap with the ones above are:

* `caused crippling damage` is a stronger sub-category of `caused major damage` - only for damage that is so crushing as
  to be most likely game-ending for the player.
* `caused damage` is active when any damage was caused - useful when you don't care whather it was minor or major damage

### Damage suffered from the player

During the players previous *and* current turn, how much damage did they inflict to the rival?

| Tag                     | Damaged inflicted by the rival                         |
|-------------------------|--------------------------------------------------------|
| `suffered no damage`    | None.                                                  |
| `suffered minor damage` | Insignificant, like a tile or two.                     |
| `suffered major damage` | Significant damage that noticably weakened the player. | 

Additional tags that overlap with the ones above are:

* `suffered crippling damage` is a stronger sub-category of `suffered major damage` - only for damage that is so
  crushing as to be most likely game-ending for the rival.
* `suffered damage` is active when any damage was caused - useful when you don't care whether it was minor or major
  damage.

### Rivals hostility towards the player

Describes the rivals current hostility towards the player, as reflected by the color and emotion on the rival emoji.

| Tag        | Meaning                                                                                   |
|------------|-------------------------------------------------------------------------------------------|
| `ally`     | Strongly motivated to cooperate with the player (green emoji). Also counts as `friendly`. |
| `friendly` | Would prefer not fighting the player (light green emoji color).                           |
| `neutral`  | No strong feelings towards the player (yellow emoji).                                     |
| `hostile`  | Eager to fight the player (orange emoji).                                                 |
| `furious`  | Extremely motivated to fight the player (red emoji). Also counts as `hostile`.            |

You can also add `was` in front of these tags (for example `was hostile`) to refer to the rivals attitude towards the
player during the **previous** turn.

Additional tags that overlap with the ones above are:

* `not hostile` = `neutral`, `friendly` or `ally`
* `not friendly` = `neutral`, `hostile` or `furious`

### The main reason for hostility

There are a few different factors that can contribute to a rivals hostility towards the player.

These tags describe which of these is the greatest contributing factor, so only one of them will be
active at any given time. If none of the contributing factors are very significant,
none of these tags will be active.

| Tag                    | Reason for hostility                                                                                                                    |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| `angered by losess`    | Recently suffered damage by the player.                                                                                                 |
| `angered by credit`    | Feels entitled to revenge for past transgressions by the player. (a broader and more long term form of memory than `angered by losses`) |
| `angered by dominance` | The player is becoming the dominant faction on the map and may soon be unstoppable.                                                     |
| `angered by rivalry`   | The player and the rival are the last two significant factions on the map, so there's no point to play politics any more.               |
| `angered by nature`    | The rivals AI personality is biased to seek out conflict.                                                                               |

### Relative strength vs the player

What is the rivals current overall power compared to the player?

| Tag                | Meaning                                                                                     |
|--------------------|---------------------------------------------------------------------------------------------|
| `much stronger`    | The player is totally insignificant compared to the rival. Active together with `stronger`. |
| `stronger`         | The rival is noticably stronger than the player.                                            |
| `similar strength` | There's not a major power difference between the player and the rival.                      |
| `weaker`           | The rival is noticably weaker than the player.                                              |
| `much weaker`      | The rival is totally insignificant compared to the player. Active together with `weaker`.   |

You can also add `was` in front of these tags (for example `was stronger`) to refer to the balance of power during the *
*previous** turn.

Additional tags that overlap with the ones above are:

* `not weaker` = `similar strength` or `stronger` or `much stronger`
* `not stronger` = `similar strength` or `weaker` or `much weaker`

### Overall situation

| Tag                  | Meaning                                                                                                                  |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|
| `common enemy`       | There is a dangerously dominant faction on the map, and it's neither the player, nor the rival                           |
| `had common enemy`   | *On rivals last turn*, There was a dangerously dominant faction on the map, and it was neither the player, nor the rival |
| `surrendering`       | The rivals are ready to offer to surrender (and the player didn't decline this yet).                                     |
| `surrender declined` | The player has declined the rivals offer to surrender (in either this or some past turn).                                |

### "Trigger context" tags

While the most common use of AI chatter is after the player selected a rival province, there are also other situations where the rival AI may say something proactively to the player.

These tags do not describe the situation on the map, but rather they describe which trigger caused the AI chatter to be requested.

| Tag                        | Meaning                                                                                                                         |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| `angry reaction`           | Rival is reacting to a hostile move by the player. (note this does NOT disqualify the rule from being used in other situations) |
| `approving attack`         | Rival is reacting postively to an attack move by the player (against a third party).                                            |
| `approving non-aggression` | Rival is reacting postively to the player choosing not to attack them                                                           |
