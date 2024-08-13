# Generating Multi-state Conformations of Ptype ATPases with a Diffusion Model

Understanding and predicting the diverse conformational states of membrane proteins is essential for elucidating their biological functions. Despite advancements in computational methods, accurately capturing these complex structural changes remains a significant challenge. 
In this study, we introduce a method for predicting diverse functional states of membrane protein conformations using a diffusion model. Our approach integrates forward and backward diffusion processes, incorporating state classifiers and additional conditioners to control the generation gradient of conformational states. We specifically target the P-type ATPases, a key membrane transporter, for which we curated and expanded a structural dataset. By employing a graph neural network with a custom membrane constraint, our model generates precise structures for P-type ATPases across different functional states. 
This approach represents a significant step forward in computational structural biology and holds great potential for studying the dynamics of other membrane proteins.

# Installation

First make sure you have all dependencies installed by running 

```
pip install -r requirements.txt
```
Our model is based on Chroma
You may need to register to obtain an access token of Chroma
https://chroma-weights.generatebiomedicines.com/


Then you can clone the project:

```
git clone https://github.com/yongwangCPH/papers/tree/main/2024/PtypeATPaseGeneration
```

# Usage

~~~python
# import package
from PtypeATPaseGenerator import *
from chroma import api
api.register_key("fdb2b9ae7e2744d1ad826cd622dc76dd") # put your token here

#Create a generator
generator = PtypeATPaseGenerator(protein_path="yourATPase.pdb",
                                 device = 'cuda',
                                 state_label = [1,0,0,0],    #Give a specific state for generation,  [E1,E1P,E2P,E2]
                                 classifier_weight = 0.75,  #The weight of classifier conditioner
                                 classifier_max_norm = 25,
                                 seq_weight = 0.5,  #The weight of sequence conditioner
                                 membrane_constraintor = True,
                                 expect_rmsd = 4,
                                 membrane_weight = 0.1  ##The weight of sequence conditioner, recommended less than 0.25
                                )
#Classify the state of Ptype-ATPase into [E1,E1P,E2P,E2]
generator.ClassifyMultistate()

#Generate the specific state for Ptype-ATPase
generator.GenerateMultistate(
                            t = 0.625 ,  #The noise scale belong to (0,1)
                            steps = 500,  #The number of denoising step
                            trajectory_length=500,
                            full_output = True,  
                            )

# Reset the state label    
generator.SetStatelabel([E1,E1P,E2P,E2])

# Reset the weight of conditioners
generator.SetConditionerWeight(classifier_weight,seq_weight,membrane_weight)
~~~

