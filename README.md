# simple way to customise your worker node on the Allora network 

![image](https://github.com/user-attachments/assets/7d9c7bf2-abc7-4bc0-9eee-840853113758) 

Following the successful release of Testnet V2 — the upgrade that brought a lot of interest to the Allora network, many crypto and AI enthusiasts use the Allora network to run worker nodes to collect Allora points in the Point Campaign.

[read about the point campaign here: https://www.allora.network/blog/launching-phase-2-of-the-allora-points-program-30832] 

The best way to maximize points from your worker nodes is to provide unique inferences to the network and contribute to its collective intelligence. Using the same model as everyone else provides little to no benefit for the network (such as the basic-coin-prediction default LinearRegression model in the official doc) will result in fewer points gained to your worker nodes.
[read more about the concept of collective intelligence here: https://www.allora.network/blog/how-allora-s-collective-intelligence-network-echoes-the-principles-of-evolutionary-biology-599c4] 
# how to easily change your model!

I will show you an easy way to change the model of your worker node so it can generate more points for you by providing unique returns and contributing to the collective intelligence of the network.
# This guide is based on the Allora-Basic-Coin-Prediction node

*You must need to buy a VPS for running Allora Worker
*You should buy VPS which is fulfilling all these requirements :
Operating System : 
Ubuntu 22.04
CPU: Minimum of 1/2 core.
Memory: 2 to 4 GB.
Storage: SSD or NVMe with at least 5GB of space.

# Clean Docker First
docker system prune (copy)
# Automatic Installtion:
cd $HOME
rm -rf basicinstall.sh
wget https://raw.githubusercontent.com/0xtnpxsgt/Allora-Basic-Coin-Prediction/main/basicinstall.sh
chmod +x basicinstall.sh
./basicinstall.sh (copy)
# Check logs
docker logs -f worker (copy)
# Before reading further please make sure that:
· Your base node is the basic-coin-prediction-node

· You have every dependency for Allora’s worker nodes installed

· Your node has been configured correctly and is running perfectly

· You know how to get inside a file on Linux CLI using nano / vim / vi whatever you prefer

Here are the easy steps:

1️⃣We are now in the basic-coin-prediction-node directory after you have configured everything to test that the basic-coin-prediction-node runs perfectly

cd $HOME/basic-coin-prediction-node (copy)

![image](https://github.com/user-attachments/assets/8cb9a76f-96d8-49e8-adbb-ca92655f10fc)

2️⃣We will get inside the model.py file — I use vim but you can use whatever command you like

vim model.py (copy)

3️⃣At the top, you will want to add numpy as np there just in case you’d need it

![image](https://github.com/user-attachments/assets/ed5d0891-1d21-4e20-ba85-c545705bb2dd)

4️⃣Next, we scroll down the def train_model section and locate the model parameter

![image](https://github.com/user-attachments/assets/c61697b6-2bc5-489f-95a1-1eef0b946072) 

5️⃣Go to the scikit-learn doc website and choose a regression model that you would like to use
Link: https://scikit-learn.org/stable/supervised_learning.html
You can use any supervised learning regression model, and each one performs the prediction differently. DO NOT PICK a classification model.

![image](https://github.com/user-attachments/assets/aff4298b-98c9-42da-b7c4-928395f65756) 

In this example, we will use the Lasso regression model.

6️⃣Click the link of your preferred model and find the usage example

![image](https://github.com/user-attachments/assets/4a0f381a-fdfb-4e81-8146-03e443b5ab2e)

7️⃣Once found, edit the model parameter in your model.py accordingly

I also added two print(..) for easier debugging just in case we have to

![image](https://github.com/user-attachments/assets/fd220014-06b3-46ad-8dae-e9de87423e80)

8️⃣Edit the library downloader at the top of the model.py too

As you can see, I commented out the LinearRegression and added the line below to the list

from sklearn import linear_model (copy)

![image](https://github.com/user-attachments/assets/b9433650-4105-4acf-82a6-9de110b0754c)

9️⃣Exit from model.py

1️⃣0️⃣Check requirement.txt if we need to add any dependencies

![image](https://github.com/user-attachments/assets/e62709bc-c640-4abe-8dce-b3a9039168ce) 

vim requirement.txt (copy


![image](https://github.com/user-attachments/assets/ff97fabe-73c7-4198-9459-7f4d3418c16e)

In this case, we don’t have to add anything


1️⃣1️⃣Rebuild the docker and restart the containers with


docker compose build
docker compose up -d  (copy)

1️⃣2️⃣If all went well, then check your inferences

curl http://localhost:8000/inference/<token> (copy)
If you see something similar to this,
![image](https://github.com/user-attachments/assets/1551c4ab-85d4-4e2b-a086-6d3b11dc7133)

Congrats! You have levelled up to level-2 builder



# Debugging
Error starting the inference container

![image](https://github.com/user-attachments/assets/1b0def94-a18f-4843-9112-a0acb34923e2) 

1️⃣Check logs to see what is going on and fix the bug accordingly or switch to another model

docker compose logs -f (copy)

2️⃣If there’s no error and you can see only the printed message that says `Begin training the model` then there’s no error, the training process just takes longer than expected

3️⃣Wait until you get `Training completed`

4️⃣Restart the other containers

docker compose up -d (copy)

5️⃣If all went well, then check your inferences
curl http://localhost:8000/inference/<token> (copy) 
# Getting code 500 instead of 200 in the logs

![image](https://github.com/user-attachments/assets/84799636-9c30-43e6-bc93-ffa4de21b8bd)

1️⃣Check your inferences


curl http://localhost:8000/inference/<token>

2️⃣A common bug is the return is a 1d array but your app.py still tries to extract it out using index and it results in an error

![image](https://github.com/user-attachments/assets/397574c1-24a4-4618-821f-cacf144b03d5)

3️⃣To fix this, we will have to get inside app.py

vim app.py (copy)

4️⃣1. Look for def get_eth_inference() and delete the [0] in the return

Before

![image](https://github.com/user-attachments/assets/b27187e0-6ded-4654-bc34-f8122bf47bd9) 

After

![image](https://github.com/user-attachments/assets/c4803c50-c878-49a5-a9fc-7dc23553c938) 

Depending on your chosen model, you may have to delete both [0]

5️⃣Rebuild and restart the containers

docker compose build
docker compose down
docker compose up -d (copy)

6️⃣If all went well, then check your inferences 
curl http://localhost:8000/inference/<token> ((copy)
# Other things you could do to further improve the model

· Change the train_dataset

· Change how the final output is aggregated

· Edit the input (X_train and X_test)

· Hyperparameter Editing / Tuning

Please see our doc for additional advice: https://docs.allora.network/devs/workers/walkthroughs/walkthrough-price-prediction-worker/modelpy.

# About the Allora Network
Allora is a self-improving decentralized AI network.

Allora enables applications to leverage smarter, more secure AI through a self-improving network of ML models. By combining innovations in crowdsourced intelligence, reinforcement learning, and regret minimization, Allora unlocks a vast new design space of applications at the intersection of crypto and AI.
















