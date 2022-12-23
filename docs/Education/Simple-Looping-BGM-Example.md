# Simple Looping BGM Example

## Intro:

The thumby API offers 2 options for playing tones, thumby.audio.play, and thumby.audio.playBlocking

thumby.audio.playBlocking is nice because we can use several of these in succession create a quick motif with only that one function and it takes care of the timing of the notes for us!

Unfortunately playBlocking also blocks all running code so we can't have a game running simultaneously. The playBlocking method is 'quick and dirty' and good for Title Screen or Game Over music, when the player
isn't immediately expectedto make any action.

If we want to have background music in our game, we need to use thumby.audio.play. But we can't stack them end-to-end like with playBlocking, because none of them will 'wait' for the prior command to finish. Instead they will each interrupt the last in quick succession causing quite a mess. So to fix this, we need to make our own simple music timing function, then place that function that within the main game loop to handle the playing of the background music.

Below we will walk you through one method of creating such a function (which was written to slot directly into the pre-loaded game SaurRun) with some explanation along the way.

## Part 1: Initializing the variables

First we will need to lay some groundwork; we'll need to code in a song using python data structures for the thumby to play. All the below items should be inserted before the main song loop. These can be broken up into three seperate steps.

### Step 1: Creating a Music Note Dictionary

This step is optional, but our Song List will be more human-readable and easier to work with if we can populate it with note names instead of frequency intergers. Basically we need to use a python dictionary (or hash table) to associate each note letter-name with is proper frequency in hertz. This one is incomplete and only has the notes we will be using for our song, but you should be able to fill in all the other notes yourself pretty easily. Later on we will pass each note name to this dictionary and it will convert them into the appropriate frequencies as expected by thumby.audio.play

```python
MusicNoteDict = { 0:40000, 
                 "C4":261,
                 "D4":293,
                 "E4":329,
                 "F4":349,
                 "G4":392,
                 "A5":440,
                 "C5":523,
                 "D5":587,
                 "E5":659,
                 "F5":698,
                 "G5":783,
                 "A6":880}
```

### Step 2: Creating our Song List

Next we will create a list that has all the notes of our song, in order. For this example I will be doing the first line to Twinkle Twinkle little star, and then repeated in a higher octive. Each item represents one 'note' of our song, and a 0 is a rest. We will be establishing the base note length or 'step' length later, which will also determine the overall tempo of the song. You don't have to neccesarily line evertying up like I did, but I find it easier to read and work with.

```python
SongList = ["C4", 0  ,"C4", 0  ,"G4", 0  ,"G4", 0  ,
            "A5", 0  ,"A5", 0  ,"G4","G4","G4", 0  ,
            "F4", 0  ,"F4", 0  ,"E4", 0  ,"E4", 0  ,
            "D4", 0  ,"D4", 0  ,"C4","C4","C4", 0  ,
            "C5", 0  ,"C5", 0  ,"G5", 0  ,"G5", 0  ,
            "A6", 0  ,"A6", 0  ,"G5","G5","G5", 0  ,
            "F5", 0  ,"F5", 0  ,"E5", 0  ,"E5", 0  ,
            "D5", 0  ,"D5", 0  ,"C5","C5","C5", 0  ]
```

### Step 3: Setting the base Note (step) Length

Here we will define how long we want individual notes to be with some simple arithmetic. We will also calculate the length of the song so that we can loop it properly in our timing function. Some games use microseconds (us) instead of milliseconds (ms) as a base timing, so just be aware which one the game uses so you can adjust accordingly. If you want adjust the notelenght to something different, you only need to edit the number on the first line 'NoteLengthMS =' and replace the 500 with whatever value you want.

```python
NoteLengthMS = 500

NoteLengthUS = NoteLengthMS * 1000 
SongLength = len(SongList) * NoteLengthUS
```

## Part 2: Writing the timing function.

Now we have all the groundwork set. We have our notes defined, our song defined, and the note lengths. Next we are going to write a function we can 'drop in' the main game loop that will handle the music. We will just need to pass it whatever variable the main game loop is already using for it's timing. We will be using the modulo operation (%) to loop it.

```python
def PlayMusic(utimeTicksUS):

     #Calculate where we are at in the song based on total playtime, loop it with modulo
    CurSongBeat = (utimeTicksUS % SongLength) // NoteLengthUS
    
     #Pull 1 note from our SongList based on elapsed time
    CurNote = SongList[CurSongBeat]
    
     #Convert the note into the frequency using our Note Dictionary
    CurFreq = MusicNoteDict[CurNote]
    
    #We can uncomment this for troubleshooting: 
    #print(CurFreq)
    
     #Tell our thumby to play our selected Frequency.
    thumby.audio.play(CurFreq, NoteLengthMS)
    return
```

## Part 3: Cleaning up and restarting the song 

