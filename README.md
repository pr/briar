# BRIAR: Basketball Reasoning via Image Analysis and code geneRation

Use Detectron2 and the Open AI API to assess NBA decision making with automatically generated, opinionated Python code. Go beyond empirical observations and rules-based evaluation with plain text queries that produce analysis. 

![intro](https://github.com/pr/briar/assets/14855334/e7fe5d56-96e9-4f94-9468-33812913d4c7)


## Examples

Stephen Curry takes the ball down the court...

https://github.com/pr/briar/assets/14855334/d0c72e69-6f88-4308-84d3-a272d5cb0a3c

What should happen next? Should he shoot? Pass? Call a timeout? We can use BRIAR to answer!

Call the decision point `initial_decision_point.png`

![image](https://github.com/pr/briar/assets/14855334/dc2916ef-0742-4eb9-a13e-3ba39787da93)

We can then run BRIAR and dynamically get its opinion on the best next move!

![image](https://github.com/pr/briar/assets/14855334/885a9367-35d4-4f9f-af98-6520fb9d47c4)

Let's see what actually happened...

https://github.com/pr/briar/assets/14855334/d79cf68b-1756-40b7-b3a8-d2a13a56ab06

He did pass!

Let's try again with a play BRIAR was not trained on...

https://github.com/pr/briar/assets/14855334/d6d8e1ce-4b0a-4916-9db1-09361e4f0995

Same question as above! What does BRIAR think should happen next? Should Kevin Durant shoot? Pass? Call a timeout? Let's see!

Call the decision point `another_decision_point.png`

![image](https://github.com/pr/briar/assets/14855334/094e2b7d-64dc-4e4a-bb4f-95567788c42a)

Let's run BRIAR again!

![image](https://github.com/pr/briar/assets/14855334/b691ef61-44db-4b66-98f1-237e5b9f3e9d)

Let's see what actually happened...

https://github.com/pr/briar/assets/14855334/d74518d6-a13d-4647-ba33-f820ea392240

He did shoot!

So how does this actually work?

## Detailed Implementation Explanation

[Detectron2](https://github.com/facebookresearch/detectron2) is a platform for object detection, segmentation and other visual recognition tasks. It's excellent, but not tailored to the NBA game. Without any training, when it looks at `initial_decision_point.png` it struggles to locate the players and the ball. 

![image](https://github.com/pr/briar/assets/14855334/b92796a2-c9d5-4749-9b99-dac01d064208)

We can train a better model though! My goal here is a proof of concept vs. actually creating a robust set of training data, so it's relatively quick to train Detectron2. The subsequent model is good enough for BRIAR to show what it can do, despite some overfitting. For model training details, please see [briar.ipynb](/briar.ipynb).

Here is the output of the model! Much better, it can tell who is on the Warriors, who is on the Lakers, and who has the ball with enough certainty to be interesting. 

https://github.com/pr/briar/assets/14855334/f46c0c47-5399-4402-b624-ab29d3fbc394

Here is what it sees in `initial_decision_point.png`

![image](https://github.com/pr/briar/assets/14855334/419f3b62-24cb-42b0-ad83-be221e3ea771)

Great! This kind of image processing is not new in the NBA context, but it does lay a strong foundation for the second part of BRIAR. 

We now have an image we can interpret, and get the state of in a dynamic way, by asking basic questions such as these:
- How many players are on the court?
- How far away from each other are the players? 
- How many players are closely guarding the player with the ball?

We can get the answers, and more, with the Open AI API! I used the GPT-3.5 Turbo model, but this should work with newer ones as well (though it will be more expensive).

You can query the API, and it will write Python code, with its own reasoning and logic, to interpret the state of the NBA game.

Let's see it in action! Let's start simple, with a basic empirical question. 

![image](https://github.com/pr/briar/assets/14855334/547bd630-874f-4c8d-b31c-0c6d94569141)

This is cool, but again nothing that new – this kind of code isn’t that hard to write, making Open AI write it instead is a marginal benefit.

The new and cool thing is reasoning, using LLMs to interpret the state, make a judgement, and reason about possible next actions. We will be able to move away from rules-based systems where we must consider every case.

Let's ask BRIAR something that requires more reasoning.

![image](https://github.com/pr/briar/assets/14855334/5125589d-fa39-41fe-ba6f-a68f04d8203b)

Wow! The key thing here is I didn’t write this code. I didn’t think of these outcomes, I didn’t tell the Open AI API that if you are being triple teamed you probably want to look for a teammate - it was able to leverage the state I gave it and its own LLM to get to that conclusion on its own. All I had to tell it was the structure of my code, the classes I wrote to help the interpretation, and it was able to generate a novel answer, that I can run in real time. 

## Limitations 

- I used a very small training set for my Detectron2 implementation, definitely overfitted.
- You still need to tell Open AI a fair amount about your code, ideally it would need less help.
- To run this code requires a powerful GPU.

## Looking Forward

Where is this going? Someone with more money and more GPUs than I will create a model trained on NBA data + NBA telecasts + coach input. It will create a real time decision engine, capable of evaluating the state of any NBA game at any moment and saying what the best next action is. This will be a paradigm shift, you will be able to ask the tool questions in plain text, no coding ability needed.

This will change the way players are coached, and the way they look at film, there will be less work for everyone. The tool will move beyond just interpreting state to actual advice and decision making, able to work at all levels of the game. It won't replace practice or innate ability, but it will be a force multiplier. I could even see a day where the tool is able to simulate and play against itself, developing new plays, figuring out how to leverage player ability in new ways, and allocate practice time as efficiently as possible. 

## Try it yourself!

You can run BRIAR easily on [Google Colab](https://colab.research.google.com/). All you need is an Open AI API key. Each run costs a fraction of a cent, so it won't break the bank. You can even train your own model! Try it today!

## Acknowledgements

BRIAR is inspired by ViperGPT, created by the Computer Vision Lab at Columbia University. I made my own implementation (with the exception of a very similar [CodexModel class](https://github.com/cvlab-columbia/viper/blob/bde4c6343825e6a131547cdfdeed8a62c9ac4b11/vision_models.py#L964C29-L964C29)), but I used a heavily inspired methodology from their [paper](https://arxiv.org/pdf/2303.08128.pdf) and [repository](https://github.com/cvlab-columbia/viper). 

## Recommended Additional Projects

- [Extracting Player Tracking Data Paper](https://assets-global.website-files.com/5f1af76ed86d6771ad48324b/5f6a64711514cb41b91b37d5_Johnson_extracting_player_tracking_data-compressed.pdf)
- [Basket Tracking Project](https://github.com/Basket-Analytics/BasketTracking)
- [Basketball Homography](https://github.com/GauravMohan1/Basketball_Homography/)

## Video Sources

- [Curry Video](https://www.youtube.com/watch?v=rL_OflGAg1M)
- [Durant Video](https://www.youtube.com/watch?v=i-CB44p1xDw)
