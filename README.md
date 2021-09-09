# Kaggle Predicting Molecular Properties
kaggle scalar coupling prediction
<h3>This is 26th place solution to Kaggle competition,  <a href="https://www.kaggle.com/c/champs-scalar-coupling"> 
Predicting molecular properties</a><br></h3>
Details about the competition:<br>
This challenge aims to predict interactions between atoms. Imaging technologies like MRI enable us to see and understand the molecular composition of tissues. Nuclear Magnetic Resonance (NMR) is a closely related technology which uses the same principles to understand the structure and dynamics of proteins and molecules.

Researchers around the world conduct NMR experiments to further understanding of the structure and dynamics of molecules, across areas like environmental science, pharmaceutical science, and materials science.
<br>
This competition was hosted by members of the CHemistry and Mathematics in Phase Space (CHAMPS) at the University of Bristol, Cardiff University, Imperial College and the University of Leeds. Winning teams will have an opportunity to partner with this multi-university research program on an academic publication
<br><br>

First of all, tons of thanks to the Kaggle community, the Kaggle team and competition hosts for the event. I am just starting out in the field (doing my bachelors now) and so if I made any mistake here or you have any suggestions, please let me know.
I took part in the competition to learn and be comfortable with Pytorch with a hands on approach. I did lots of tensor manipulation. I did not have much expectation(it is intimidating to see so many data science experts, also I joined the competition late). And then I got a medal… <br>
For details about data, see notebook **molecules-eda.ipynb** of this repo or at <a href="https://nbviewer.jupyter.org/github/nupam/kaggle-predicting-molecular-properties/blob/master/molecules-eda.ipynb">Nbviewer</a><br>
<br>
### Model details
Too much talk, real deal is as follows:<br>
I trained one model responsible for calculating J-coupling(referred as JC next) of all types. The model produced 8 outputs, one each for a type, of which 7 of them were in don't care condition for any input. I directly optimized the evaluation metric, using it as loss.<br>
Like many teams I used an attention based NN model (Fast.ai's multi-head-attention of text module was an very important piece), to generate features and concatenated it with other engineered features like from 'distance is all you need' kernel. Wikipedia helped me generate few more features. Overall, it is just a variant of BERT.<br>
Atoms of the molecule whose JC was to be found were fed to the model one by one as input steps, zero padded 30 input steps. I added a classification token(well, actually a series of numbers that represented the learned token) as the first input step (time step sounded weird and atom step weirder) about which I did not know what would be suitable so I trained a small NN based on simple features like input type. All other input step consisted of features that was concerned with a particular atom. Each time step had a binary feature which told the model if the atom for which JC was to be computed. Features were collected from the attention network by concatenating output of first input step (say special input step, due to lack of better name, I think I need to be better with names) maximum and average of its output along all input steps with outputs from steps of atoms whose JC was to be calculated, all total 5X length of encoded output of any input step). These with hand engineered features (after going through few residual layers) were passed through another residual layer and then to a feed forward layer to generate 8 outputs, which was then trained with Adam optimizer using cyclic learning rate.

### Training details
At first I trained a models on the Kaggle kernel itself, and the best I was able to reach was -2.015 LB score in those 9 hours, batchsize of 1024 was used.<br>
I trained the final model on GCP (thank you for those free credits) for around 30 hours with a batchsize of 2048 on a V100 GPU, gradients were noisy with smaller batches. I did not do oversampling, the loss function took care of it. But, I did scale the embedding gradients based on their frequencies. I could not train it any more, credits were gone, and the competition was ending too. But 30 hours of training gave lb score of -2.607 from a single model.<br>
Later, I fine tuned the model for first three types(time was up) on Kaggle kernel by accordingly adjusting the weights for the respective type of JC. It gave me boost of around -0.015, not that it would have changed my final rank😁.
<br>
I learned a lot, read research papers, blogs, watched Jeremy Howard's lecture videos.<br>
It was fun.<br>

Also, if you have any suggestions, or any thing at all, leave a remark.<br>
I will be adding more documnetations as soon I find some free time.<br>
---
***update after a longg time: ping me if any doubt***
I can be found on kaggle at <a href="https://www.kaggle.com/greenahn"> greenAHN</a>.
