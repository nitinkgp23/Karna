### Title

Neural Machine Translation by Jointly Learning to Align and Translate

### tl;dr

Usage of "Attention layer" in NMT gives better results compared to the results of encoder-decoder model(seq-seq) which encodes the entire sentence into a single vector and passes to the decoder for translation 

### Describe the method

BACKGROUND:
The proposed model was built on encoder-decoder model given by(cho.et.al) and thus primarily contains two things an encoder which takes the input and decoder which gives the output.The probability of output can be represented in mathematical form as
                                    p(y)=![img](http://latex.codecogs.com/svg.latex?%5Cprod_%7Bt%3D1%7D%5E%7Bn%7D%28P%28yt%2F%7By1%2C..yt-1%2Cc%7D%29%29) {y={y1,y2,y3...yn} the output,x={x1,x2,...xn}is the input}
where
   
   ![img](http://latex.codecogs.com/svg.latex?P%28y_%7Bt%7D%2F%7By_%7B1%7D%2C..y_%7Bt-1%7D%2Cc%7D%29)=![img](http://latex.codecogs.com/svg.latex?g%28s_%7Bt%7D%2Cy_%7Bt-1%7D%2Cc%29) where y_{t}-output at time t
                                              y_{t-1}-output at time t-1
                                              s_{t}-hidden state at time t
                                              g-nonlinearity
                                              c-context vector

The encoder encodes the obtained sentence and encodes it into a context vector(here it is the hidden state of the last cell of encoder) and passes it into decoder and the decoder produces the output until a special character is obtained.

Sequence to Sequence models perform well over shorter sentences but fail at translations when it comes to encoding sentences which are too long especially when the length of testing data is more when compared to the sequences in training data But the attention model proposed eliminates this single vector encoding but rather finds an encoding for every time step of decoder using attention and eliminates the above problem  
![alt text](https://raw.githubusercontent.com/coffeelover12111997/randomfiles/master/seq2seq.png)

The above pic is an older version of encoder-decoder later they provided the context vector as inputs to different output(decoder) cells

THE MODEL:

![alt text](https://raw.githubusercontent.com/coffeelover12111997/randomfiles/master/attentionNMT.png)    

In the proposed architecture we have 
                            ![img](http://latex.codecogs.com/svg.latex?P%28Y_%7Bi%7D%2F%7BY_%7B1%7D....Y_%7Bi-1%7D%2CX%7D%29%3Dg%28s_%7Bi%7D%2CY_%7Bi-1%7D%2Cc_%7Bi%7D%29) #probability of output at ith decoder
where s_{i} is given by 
                            si=![img](http://latex.codecogs.com/svg.latex?f%28s_%7Bi-1%7D%2CY_%7Bi-1%7D%2Cc_%7Bi%7D%29) where s_{i}-hidden state at time i
                                                      Y_{i-1}-output at i-1 th decoder
                                                      c_{i}-context vector corresponding to ith output

and ![img](http://latex.codecogs.com/svg.latex?c_%7Bi%7D) is computed as  ![img](http://latex.codecogs.com/svg.latex?c_%7Bi%7D)=![img](http://latex.codecogs.com/svg.latex?%5Csum_%7Bj%3D1%7D%5E%7Bn%7D+%5Calpha_%7Bij%7D%2Ah_%7Bj%7D)(where j corresponds to jth cell of encoder,n-length of i/p sequence)

The above method is a soft attention model i.e the output ci is a weighted sum of all the encoder hidden states and backpropogation can be applied effectively to such a model (but if the attention model is hard then only the inputs with higher probability are only selected and thus back propogation would become difficult. In such cases RL is used for training
example paper-Recurrent Models of Visual Attention)

so in the above context vector equation ![img](http://latex.codecogs.com/svg.latex?%5Calpha_%7Bij%7D) is given by             (where j-jth element of input sequence,i-ith output)
                                ![img](http://latex.codecogs.com/svg.latex?%5Calpha_%7Bij%7D)= exp(![img](http://latex.codecogs.com/svg.latex?e_%7Bij%7D))/( ![img](http://latex.codecogs.com/svg.latex?%5Csum_%7Bk%3D1%7D%5E%7Bn%7D+exp%28e_%7Bik%7D%29))

where ![img](http://latex.codecogs.com/svg.latex?e_%7Bij%7D%3Da%28s_%7Bi-1%7D%2Ch_%7Bj%7D%29) and a here is an alignment(here it means to select the input sequence in such a way to obtain the  required output)model that scores how well an input is related to the output

Here in the paper a is represented by a feed forward network and is trained along with encoder-decoder network.

Thus the ![img](http://latex.codecogs.com/svg.latex?%5Calpha_%7Bij%7D) determines how well the network pays attention to an input at j and thus this removes the need for encoding the entire sequence to a single vector

Also In this model a Bi-directional RNN is used for the encoder part compared to the single directional used for seq-seq paper
and in this rnn h_j=[h_forward;h_backward](backward and forward hidden states are concatenated and this gives a better result compared to a normal RNN


DATA USED AND RESULTS:
They used WMT 14 english to french translation parallel corpus which contained Europarl(61M words),news commentary (5.5M),   UN (421M) and two crawled corpora of 90M and 272.5M words respectively,totaling 850M words.They reduced the size of
the combined corpus to have 348M words using the data selection method by Axelrod et al.(2011).
They concatenated news-test-2012 and news-test-2013 to make a development (validation) set, and test set consists of 3003 sentences not present in the training from WMT 14.

They used two models for comparison 1)RNN encoder-decoder(cho.et.al) 2)their proposed model.Both the models had 1000 hidden units and a maxout hidden layer(some what similar to maxpooling but works on a 1d vector) 1st model had enc-dec length of 30 while the 2nd model has enc-dec length of 50.They used a mini batch SGD optimizer with Adadelta.

BLEU (bilingual evaluation understudy) scores are summarized in the table

![alt text](https://raw.githubusercontent.com/coffeelover12111997/randomfiles/master/results.png)
                




### Any further details
Some papers which are extended from this and have better results
1)Attention Is All You Need
2)End to End Memory Networks 

### My two cents

Since this is a very old paper many advancements have been made to this and attention has gained so much popularity in dl community that people tried using it for most of the tasks I could think of.Stacked attention layers independent of rnn and cnn have been used to make SOTA models.I liked the idea of selecting specific inputs to obtain outputs almost like how our human mind does  
