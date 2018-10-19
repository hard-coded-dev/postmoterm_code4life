# Code4Life Post-mortem 
https://www.codingame.com/multiplayer/bot-programming/code4life
#93/2366

 I am grateful to codingame.com that introduced a very interesting game. I really enjoyed and dedicated it for a week. I basically implemented heuristics with FSM based.
The most difficult and import part I thought were what sample rank should I get and what kind of molecule type should I pick up for myself or blocking the opponent.

### Sample Rank
 I estimated expected average cost based on my expertise and magic number. My thought and some experiments showed me about 4~4.5 average cost per sample would be good to me and my heuristics. I checked sample pools that were given in the referee code and removed one already popped out, then calculated average estimated cost( sample cost - expertise ) of each rank and find best fit with my budget. I also tried to check with my current storage to find more suitable cost, but I didn’t work well since it would often return lower rank than I expect.

### Molecule Type
 First, I just wrote a simple logic to collect one first both robot requires. It seems worked fine until certain levels but I couldn’t go ahead with it. But I didn’t have enough time to change my algorithm such as minimax or so. 
I finally thought another logic that calculates very last safe turn to protect collecting molecules from the opponent and vice versa. That algorithm very depends on where the opponent is.
 Basic formula is as below.

**very safe turn = molecule available - required sample cost. ( cost - expertise )**

 ex) available : A4B4, cost A3B2 : distance = 4-3 + 4-2 = 3

This means You can collect all of required molecules if you have 3 more turns. Of course you can’t complete it if your storage or available molecules are not enough. let distance as big score in this cases.
What if the opponent is already in the molecule module or on the way? The available resources should be divided by 2 in that case.

**safe turn = molecule available / 2 - additional cost for the sample.**

 ex) available : A4B4, cost A3B2 : distance = 4/2-3 + 4/2-2 = -1, impossible to collect them safely.

It also possible to calculate even if the opponent is on the way to the molecules.( remainder of divide by 2 is rounded off due to concurrent rules )

**safe turn = eta + (molecule available - eta) / 2 - sample cost**

ex1) available : A4B4, cost A3B2, eta : 1 : distance = 1 + (4-1)/2-3 + 4/2-2 = 0. it’s still collectable if you choose A first.
 But should be careful to collect correct one while the opponent is coming.

ex2) available : A4B4, cost A3B2, eta : 2 : distance = 2 + (4-1)/2-3 + (4-1)/2-2 = 1.

ex3) available : A4B4, cost A3B2, eta : 2 : distance = 2 + (4-2)/2-3 + 4/2-2 = 0, be careful not to take A twice that led you lose one turn.

 Finally, you can calculates all of safe turns of yours and the opponents’ and sort it as the lowest( greater or equal than 0 ) should be in urgent whatever it’s yours or not.
Unfortunately, This logic was submitted at very last minutes so that I couldn’t have a time for tuning or evaluation, I even couldn’t verified well whether it works correctly. I was very disappointed that I couldn’t have any chance to confirm my theory was correct or worthy. 

 I also would like to describe somewhat interesting points for each module. General tricky parts all over the modules is estimating the last doable turn on each module not to make useless action, for example you must not request new sample data over 191 turns since there is no time to produce it.

## Sample Module
- Request as much as possible with estimated sample rank. But you should keep spare room if you want retrieve existing sample data from the cloud.

## Diagnosis Module
- There is nothing special in this module but you may need to always check items in the cloud to find better one, especially you are about to achieve science project as you need a specific expertise gain bonus.

## Molecule Module
- I already briefly described as above what kind of molecule type you may pick up.
- You may go back to the diagnosis module If you can’t complete any sample data and store the least suitable one compare to your storage.
- You can replace sample data with another one in the cloud if it is better or easier than existing one. I would recommend you to pick up required molecules first before replacing item to protect blocking from the opponent.

## Laboratory Module
- When you have more than one sample to be produced, you should check its expertise gain first and it would give you an another benefit that lead you one less molecule for the next sample or even make it possible to produce new one that could not have been possible. There are only 6 cases to a check proper order at maximum.
- A simple tricky part is checking the opponent’s status before producing medicine and just wait at the module if he is waiting for returning molecules and I am potentially leading( my score + sample’s score >= opp’s score + opp’s completed sample score ) the game.
- One thing I still couldn’t make a clear idea is where to go either molecule module or sample module when you have sample data left.


Thanks,
