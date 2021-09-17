# Symmetry-Sync solved with AGANA for Weizmann Institute

The original purpose of this study is to prove wrong one of the assumption of the Standard Model of particles, the conservation of the leptonic flavor.
We are provided by the CERN data arranged as images 28 x 28 x 1.
Each "image" is a matrix of number which symbolize the number of events of a certain measure we decide to study. (for ex the mass of the new particle created by an "fusion" of muon and an electron called MuE). 
When the muon has more energy than the electron it s called muon-electron (MuE) and electron muon when not (EMu).
The theory says that we should have approximatively the same numbers of event of MuE and EMu.

Each event is coming from a simulation (ie Poisson, when different values of lambda meaning different background). 

The goal is to detect a signal without knowing his form, and if there is any at all, inside a background, which means we have a number of events MuE (for ex) way too low (large) compared to the number of EMu.

For the purpose of this study we call background (bkg) the difference of two "images of events" took randomly from our sample. We ll explain why later.
We call background + signal (sig) the difference of two "images of events" one from the background and one from the background + signal.

Since the problem is a bit tricky to solve, and could lead us to use as many Neural Network as there is background to learn, we had to reformulate it.

For ex, its like having pictures of chairs, some with, let's say a fly on the picture (which the model should label as "correct" and return a small error) and some have let's say five chair legs (that should be labeled as "not correct" by the model).

Reformulated, the goal is to find when there is an anomaly without knowing the form of the anomaly or if there is any at all (here the fly is not an anomaly, but 5 legs in the chair is) and without any assumption on the background (here it is a chair, but it can be a table, a hat...).

To solve this issue there were three steps:
- First we had to find a way to get rid of the background dependance issue
- Second find the correct model(s) and train it on bkg only
- and eventually pass, through the model, data with signal (in red) and data without signal (in blue, see it as validation set) in order to see if the model succeed to separate the distribution of the two sets of errors

To solve the first point, we had the idea to work on a difference of images normalized and not on the raw image (we took two images from the bkg and subtract one from another and then divide by five times the square root of the sum of the two matrixes in order to normalize the results to be between -1 and 1).

This idea relies on the fact that the error that the model should retrieve on data without signal should be lower than the error the model retrieves when there is a signal. 
We gonna train the model we chose on bkg only in order for him to learn the bkg and then when we will pass through the model data with bkg only (validation set) the error distribution should be clearly separated from the error distribution the model made on data with signal.

Since we took the difference of two pictures, what is inside the picture is not important anymore.

To solve the second point we had the idea to use Neural Networks that will learn the bkg only.

We decide to use at first, an Autoencoder to learn the bkg (data without signal).
Then we pass, through this neural network, data with signal (in red) and data without signal (in blue, see it as validation set). The results (on the first graph) were not satisfying enough.

So, we tried to implement a GAN in order to train the Discriminator (Disc) to learn how to separate errors, but the results were not as good as expected.

And then we had the idea (that maybe already exist) to combine the two models by replacing the Generator by an Autoencoder that was already pre trained a bit on the bkg only.
(the goal of the Generator is to disturb the Discriminator by sending to it random pictures that eventually should tend to look like the input, but if the Discriminator is too strong he will "kill" the Generator and the Generator wont be able to simulate image that look like the input). 
Both Discriminator and Autoencoder can be used now to measure the power of separation between bkg and bkg + signal, and with the ROC curve results we should be able to quantify the performance of each model to eventually find the perfect one through grid search.

The two graphs below represent the separation that the same Autoencoder done, before (first graph) and after (second graph) training it against the Discriminator (same idea as the original GAN).

We can see that after that we trained the Autoencoder against the Discriminator the separation the Autoencoder succeed to do (distribution of the error made on validation set and test set where validation is like bkg and test is bkg+signal) is better.

We can now enhance the Autoencoder by tweaking the hyperparameters and training it again against the Discriminator.

Moreover, it works no matter which bkg we took (for ex it could be shoes, hats, chairs... no matter what, our model will recognize if there is an anomaly).

The new model is called AGANA.

New Physics here we come ;°)
