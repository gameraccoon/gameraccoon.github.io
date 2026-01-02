+++
date = '2020-03-02T07:29:29+01:00'
draft = false
title = 'Borderline Postmortem'
type = "blog"
+++
![](/projects/borderline-render.png)

[Borderline](https://igorsgames.itch.io/borderline) is a game developed for [8 bits to Infinity game jam](https://itch.io/jam/asymmetrical-jam) for 7 days by a team of 4 people:

* Pavel Grebnev (me) - Programming
* [Igor Shimanskiy - Programming &amp; AI](https://igorsgames.itch.io/)
* [Egor Ivanitsky - Art &amp; Level Design](https://www.artstation.com/resed)
* [Bernard Machado - Sound &amp; Music](https://soundcloud.com/nudsmachado)

We live in different cities (and countries) but sometimes collaborate online to make a game in a limited time, for a lot of fun and experience. This was my second time with the team, the previous time we worked on a 2D platformer ([link](https://ldjam.com/events/ludum-dare/38/catsorbed)).

The topic of the jam this time was **Asymmetrical Gameplay**. At the start of the jam the theme was revealed: **Two Alternate Dimensions**.

#### Preparations

Before the jam, we mainly did two things:

* Made ourselves familiar with the tools that we were going to use
* Discussed ideas of asymmetrical games that we were able to implement in 7 days

We chose UE4 as the engine and decided that we were going to make a 3D game this time.

As for the game ideas, we discussed roughly twelve: time managers, multiplayer shooters, puzzles, stealth actions, and more. The only issue was that all the "asymmetrical" ideas we discussed had a mandatory multiplayer part, which would make them hard to be tested by the judges. We decided to keep it as simple as possible and to make a local multiplayer game with a shared screen if we didn't come up with a singleplayer idea.

As for preparation, before we knew Egor was joining us (so we weren't sure there'd be someone who could model and animate), I was investigating Blender and the animation pipeline for Blender => UE4.

{{< video src=200201_1959.mp4 controls=yes loop=yes >}}

We were also investigating how to make a local multiplayer with the shared camera, and how to reassign controls on the fly (so the players can choose whether they want to use two gamepads, a keyboard and a gamepad, or play on a single keyboard together).

{{< video src=200229_1452.mp4 controls=yes width=600 >}}

#### Theme reveal and jam start

After the theme of the jam was revealed and the jam started, we made a pause for an hour so everyone could think about their own ideas. Then after the pause, we gathered together in a conference call, presented our ideas, listed them all (8 ideas in total), and voted for the best idea.

The idea that won our votes was a first-person horror game about a man trapped in a haunted abandoned mental hospital, and who can see ghosts only in mirrors (so they're invisible in reality).

It was already night for most of us, so we decided to have a good sleep before starting productive work in the morning.

### Development process

We chose the UE4 FPS template as a starting point and split the game into domains:

* I took the player character, camera, controls, weapon, and mirror
* Igor took enemy characters, AI, and their interaction with the player
* Egor took making all the models, animations, and levels (then also effects)
* Bernard took making music and sounds

We used Discord as a communication tool, git as the collaboration tool for programmers, and Dropbox to exchange big files and builds.

At the start of the first day, we made the mirror and base enemies that moved towards the player.

{{< video src=200215_1340.mp4 controls=yes width=600 >}}

#### Reflection-dependent materials

The interesting part was to make objects to be visualized differently in the world and in the mirror. To achieve that, each frame we saved the position of the player camera, in the object materials we added calls to a special material function that compared the saved camera position with the camera position that this object is being rendered from. If they match, it means we're rendering from the player's perspective, otherwise, it's the mirror reflection perspective. We used this info to set different material parameters for each case.

![](VirtualMirrorCamera_inverted.png)

#### First days results

The first two days (Saturday and Sunday) we worked from early morning to late evening. During these two days, we made the main gameplay elements:

* Enemies that follow and attack the player
* The mirror: the model and switching between the mirror and the gun
* The gun: damage, ammo handling, HUD
* Music and basic sounds
* Test level to be able to test all the mechanics of the game together
* Menu

{{< image_maximize path=200229_173609_AsymmetricalJam_Game_Preview_Standalone_64-bitPC.png width=600 >}}

For the next four days, Egor was making all the models and the animations and we were gradually getting rid of default content, and finally replaced all models, anim-blueprints and other default classes with our implementations.

At the same time, we were polishing the gameplay, improving the AI, and adding new sounds to the game.


#### Using explicit render to texture for the mirror

One concern we had these days was the bad quality of the reflection and no control over it. From the beginning, we used planar reflection capture which is an easy way to make planar reflections because it handles all the math for the developer. Although it is very good in some cases, for us it turned out to be not a very handy tool. After all, we decided to use explicit render to texture with our own calculations underneath. That improved the reflection quality dramatically.


#### Revolver mechanics

The second big concern was gun handling. We had two opinions on this:
On one side we wanted to give the feeling of cocking the hammer of a revolver by the thumb (somewhat like R8 double-action revolver from CS:GO).
On the other side, this feature was dangerous because most of the players aren't used to this way of shooting in the games.
In the end, we decided to keep this feature.

Another interesting part of the gun is that Egor made 6 different animations for each shot, so the drum (that has non-symmetrical textures) was always in the correct position, plus the animations of the revolver movement were a bit different. We also had 6 sockets where the correct amount of rounds was placed. With all that together, if you load 5 rounds, you'll see how these rounds are being shot and rotated exactly as you would see it with a real revolver.

{{< figure_start >}}
{{< video src=200229_2013.mp4 controls=yes loop=yes width=500 >}}
{{< figure_caption text="You can note a cross on the drum during the first shot" >}}
{{< figure_end >}}

Here's the view of the animation state machine that resolved all the shooting and rollback transitions between states (it also automatically resolves the reloading and some corner animation desynchronization cases).

{{< image_maximize path=200229_182203_GunAnimInstance.png width=600 >}}

#### Sound and music

From Bernard:

> In the sound process, I started composing the ambience, which is made of sounds and randomized musical elements like chords in different octaves and short melodies. Then for the sound effects, all of them were edited, equalized and processed in Ableton Live (Digital audio workstation) and after they were done, everything went to Fmod (middleware). In Fmod the sound events were configured to sound as they’re sounding in-game, with looping, randomness and other behaviors that videogames media demands. The final stage was inside Unreal. After the programmers set the events in the game scenario, I was able to connect Fmod to Unreal in real-time, allowing me to make the fine adjustments. Plus: for the Ending Theme I tried to keep the mood close to the in-game ambience.

{{< image_maximize path=asym-sound1.png width=600 >}}
> On the left side of the picture, there are all the sound events in the game

{{< image_maximize path=asym-sound2.png width=600 >}}
> This one has the mix groups with the VCAs that later became Music volume and SFX volume in options

{{< image_maximize path=asym-sound3.png width=600 >}}
> A print of Ableton Live

#### Lighting performance issue

On the last day, we were able to play the final version of the level. We found out that the game was having very low FPS on some of our PCs. After a short investigation, we found out that the problem was in the lighting. Not having enough time to fix it properly we decided to remove the lights from the level and add a flashlight to the player.

There are two flashlights that were added: one in the direction of the player's camera view, and another in the direction of the reflection RTT camera (so the player could see something in the mirror). We made the second flashlight stronger to motivate the player to use the mirror to see longer distances.


#### Final steps

AI was changed a lot on the last day after testing on the final map. Enemies become faster making it harder for the player to just run through the level.

Before the submission deadline, while Igor was adding enemies to the level, Egor and I were polishing materials, effects, and menus.

### The results

After the game submission, we sent the link to our friends and colleagues to see their reactions and comments to the game. Some of them recorded their plays, which was a very helpful way to see how they reacted to the game mechanics and also to notice some bugs that hadn't been discovered before.

One thing that definitely felt good: the game had the desired atmosphere.

(videos with sound)

{{< video src=vod-555701234-offset-1108-convert-video-online.com_.mp4 controls=yes width=600 >}}
{{< video src=Ispygavcb.mp4 controls=yes width=600 >}}

### Things that went good

* We made it to the deadline :)
* We implemented the majority of the features that were planned
* The game content is superb
* Most of the time we worked and collaborated exceptionally productively
* The main mechanics and AI work well (at least most of the time)
* Settings were very useful for streamers: inverse Y-axis, mouse sensitivity, sound volume, and music volume

### Things that went bad

* One of the themes of the jam was not covered enough (asymmetrical gameplay)
* It's not clear for the players, without pointing it out to them explicitly, that they need to use the mirror
* Players can avoid using the mirror, they can just lure ghosts and shoot at them in the direction reversed to the movement
* The shooting mechanics feel not correct and annoying for some players, they feel that they interact with the gun correctly but still can't shoot, also they feel that the gun mechanics are not realistic
* Game performance is still bad on some machines
* The reloading interruption feature is being triggered accidentally all the time and is never used as intended

### What could have been improved

For each point above

* Choose another game idea or make the switching between mirror and gun and also switch something else in the gameplay or visuals
* Make the mirror present by default or show both the gun and the mirror at the same time, make a tutorial level
* Make the ghosts deal more damage or make zombies invisible as well
* Choose more classical shooting mechanics, make the hammer cocking animation after each shot (not before)
* Trade some development time to improve performance
* Remove the releasing interruption feature

### Things that were cut

We had a door prop with animation that we decided not to use because it was added on the latest stages, had unresolved issues (how the door animation should react if there's something on the way), and because of the risks of interfering with the AI.

{{< video src=200229_1940.mp4 controls=yes width=600 >}}

The zombie character was modeled more realistically, but we decided that it was risky and **cut it**.

{{< video src=200229_2000.mp4 controls=yes width=600 >}}

Link to the game: [https://igorsgames.itch.io/borderline](https://igorsgames.itch.io/borderline)