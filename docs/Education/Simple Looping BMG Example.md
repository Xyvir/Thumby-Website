#Simple Looping BGM Example

The thumby API offers 2 options for playing tones, thumby.audio.play, and thumby.audio.playBlocking

thumby.audio.playBlocking is nice because we can use several of these in succession create a quick motif with only that one function and it takes care of the timing of the notes for us!
Unfortunately playBlocking also blocks all running code so we can't have a game running simultaneously. This method is 'quick and dirty' and good for Title Screen or Game Over music, when the player
isn't immediately expectedto make any action.

If we want to have background music in our game, we need to use thumby.audio.play. But we can't stack them end-to-end like with playBlocking, because none of them will 'wait' for the prior command to finish. Instead they will each interrupt the last in quick succession causing quite a mess. So to fix this, we need to make our own simple music timing function, then place that function that within the main game loop to handle the playing of the background music.

Below we will walk you through one method of creating such a function (which can slot direction into SaurRun) with explanations along the way.

# Step 1: Creating a MusicNote Dictionary

This step is optional, but it will be more human-readable and easier to work with if can use note names instead of frequencies to make our song. Basically we need to use a python dictionary (or hash table) to associate each note name with is proper frequency it hertz. This one is incomplete and only has the notes we will be using for our song, but you should be able to fill in all the other notes yourself pretty easily. Later on we will pash each note name to this dictionary and it will convert them into the appropriate frequencies as expected by thumby.audio.play


