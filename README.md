# Internship-Problem-Sign-Recognition
the traffic sign board using German traffic sign recognition benchmark (GTSRB) in various classes.
https://akshyatkashyap.github.io/Internship-Problem-Sign-Recognition/
{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 1,
   "id": "ade6fe91",
   "metadata": {},
   "outputs": [],
   "source": [
    "#Importing Libraries\n",
    "import numpy as np \n",
    "import pandas as pd \n",
    "import matplotlib.pyplot as plt\n",
    "import cv2\n",
    "import tensorflow as tf\n",
    "from PIL import Image\n",
    "import os\n",
    "Root = \"D:\\Traffic_Sign_Recognition\"\n",
    "os.chdir(Root)\n",
    "from sklearn.model_selection import train_test_split\n",
    "from tensorflow.keras.utils import to_categorical\n",
    "from keras.models import Sequential, load_model\n",
    "from keras.layers import Conv2D, MaxPool2D, Dense, Flatten, Dropout"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "id": "70c02523",
   "metadata": {},
   "outputs": [],
   "source": [
    "#Storing data and labels in lists\n",
    "data = []\n",
    "labels = []\n",
    "classes = 43\n",
    "cur_path = os.getcwd()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "id": "11847d5b",
   "metadata": {},
   "outputs": [],
   "source": [
    "#Retrieving the images and their labels \n",
    "for i in range(classes):\n",
    "    path = os.path.join(cur_path,'train',str(i))\n",
    "    images = os.listdir(path)\n",
    "    for a in images:\n",
    "        try:\n",
    "            image = Image.open(path + '\\\\'+ a)\n",
    "            image = image.resize((30,30))\n",
    "            image = np.array(image)\n",
    "            #sim = Image.fromarray(image)\n",
    "            data.append(image)\n",
    "            labels.append(i)\n",
    "        except:\n",
    "            print(\"Error loading image\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "id": "13e4dc74",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "(39209, 30, 30, 3) (39209,)\n"
     ]
    }
   ],
   "source": [
    "#Converting lists into numpy arrays\n",
    "data = np.array(data)\n",
    "labels = np.array(labels)\n",
    "print(data.shape, labels.shape)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "id": "9591802e",
   "metadata": {},
   "outputs": [],
   "source": [
    "#splitting data with the help of sklearn function train_test_split() into training and testing data.\n",
    "X_train, X_test, y_train, y_test = train_test_split(data, labels, test_size=0.2, random_state=42)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "id": "f72777ef",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "(31367, 30, 30, 3) (7842, 30, 30, 3) (31367,) (7842,)\n"
     ]
    }
   ],
   "source": [
    "print(X_train.shape, X_test.shape, y_train.shape, y_test.shape)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "id": "e15c4e31",
   "metadata": {},
   "outputs": [],
   "source": [
    "#Converting the labels into one hot encoding\n",
    "y_train = to_categorical(y_train, 43)\n",
    "y_test = to_categorical(y_test, 43)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "id": "92a6fc74",
   "metadata": {},
   "outputs": [],
   "source": [
    "#Building CNN Model\n",
    "model = Sequential()\n",
    "model.add(Conv2D(filters=32, kernel_size=(5,5), activation='relu', input_shape=X_train.shape[1:]))\n",
    "model.add(Conv2D(filters=32, kernel_size=(5,5), activation='relu'))\n",
    "model.add(MaxPool2D(pool_size=(2, 2)))\n",
    "model.add(Dropout(rate=0.25))\n",
    "model.add(Conv2D(filters=64, kernel_size=(3, 3), activation='relu'))\n",
    "model.add(Conv2D(filters=64, kernel_size=(3, 3), activation='relu'))\n",
    "model.add(MaxPool2D(pool_size=(2, 2)))\n",
    "model.add(Dropout(rate=0.25))\n",
    "model.add(Flatten())\n",
    "model.add(Dense(256, activation='relu'))\n",
    "model.add(Dropout(rate=0.5))\n",
    "model.add(Dense(43, activation='softmax'))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "id": "c3fee04a",
   "metadata": {},
   "outputs": [],
   "source": [
    "#Compilation of the model\n",
    "model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "id": "079c0a13",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Epoch 1/15\n",
      "981/981 [==============================] - 76s 57ms/step - loss: 3.6388 - accuracy: 0.2982 - val_loss: 0.4276 - val_accuracy: 0.8753\n",
      "Epoch 2/15\n",
      "981/981 [==============================] - 55s 56ms/step - loss: 0.7417 - accuracy: 0.7883 - val_loss: 0.1936 - val_accuracy: 0.9459\n",
      "Epoch 3/15\n",
      "981/981 [==============================] - 56s 57ms/step - loss: 0.4804 - accuracy: 0.8635 - val_loss: 0.1107 - val_accuracy: 0.9727\n",
      "Epoch 4/15\n",
      "981/981 [==============================] - 56s 57ms/step - loss: 0.3636 - accuracy: 0.8963 - val_loss: 0.0871 - val_accuracy: 0.9783\n",
      "Epoch 5/15\n",
      "981/981 [==============================] - 56s 57ms/step - loss: 0.3273 - accuracy: 0.9050 - val_loss: 0.0643 - val_accuracy: 0.9824\n",
      "Epoch 6/15\n",
      "981/981 [==============================] - 56s 57ms/step - loss: 0.2664 - accuracy: 0.9236 - val_loss: 0.0582 - val_accuracy: 0.9856\n",
      "Epoch 7/15\n",
      "981/981 [==============================] - 56s 57ms/step - loss: 0.2435 - accuracy: 0.9316 - val_loss: 0.0880 - val_accuracy: 0.9748\n",
      "Epoch 8/15\n",
      "981/981 [==============================] - 56s 57ms/step - loss: 0.3246 - accuracy: 0.9100 - val_loss: 0.0567 - val_accuracy: 0.9837\n",
      "Epoch 9/15\n",
      "981/981 [==============================] - 55s 56ms/step - loss: 0.2453 - accuracy: 0.9333 - val_loss: 0.0443 - val_accuracy: 0.9869\n",
      "Epoch 10/15\n",
      "981/981 [==============================] - 56s 57ms/step - loss: 0.2387 - accuracy: 0.9354 - val_loss: 0.0548 - val_accuracy: 0.9828\n",
      "Epoch 11/15\n",
      "981/981 [==============================] - 55s 56ms/step - loss: 0.2504 - accuracy: 0.9359 - val_loss: 0.0693 - val_accuracy: 0.9800\n",
      "Epoch 12/15\n",
      "981/981 [==============================] - 55s 56ms/step - loss: 0.2188 - accuracy: 0.9440 - val_loss: 0.0433 - val_accuracy: 0.9875\n",
      "Epoch 13/15\n",
      "981/981 [==============================] - 55s 57ms/step - loss: 0.2157 - accuracy: 0.9401 - val_loss: 0.0554 - val_accuracy: 0.9832\n",
      "Epoch 14/15\n",
      "981/981 [==============================] - 55s 56ms/step - loss: 0.2179 - accuracy: 0.9470 - val_loss: 0.0521 - val_accuracy: 0.9846\n",
      "Epoch 15/15\n",
      "981/981 [==============================] - 57s 58ms/step - loss: 0.2370 - accuracy: 0.9373 - val_loss: 0.0548 - val_accuracy: 0.9846\n"
     ]
    }
   ],
   "source": [
    "#Training of the model\n",
    "epochs = 15\n",
    "history = model.fit(X_train, y_train, batch_size=32, epochs=epochs, validation_data=(X_test, y_test))\n",
    "model.save(\"my_model.h5\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "id": "474dc6ec",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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WEE6CfGe/REY0696SwTt7e29vvell15i0KBBve7zk5/8hJNPPvlgw4uItra2SIcAeLjX0MZyH9NH5UQ6DGMi4q5/fMq67XV9esxJQ7O444wjetx+66238tlnnzFlyhROOeUUvvWtb3HXXXdRVFTE2rVrWbduHWeffTZbt26lqamJm266iWuuuQaA4uJiVq1aRX19PaeddhrHHXcc//73vxk2bBjPP/88qampXH755Zx++unMmzeP4uJiLrvsMv7xj3/Q2trKU089xYQJE6iqquLiiy+mpqaGmTNnsmzZMlavXk1+fv4+sV533XWsXLmSxsZG5s2bx1133QXAypUruemmm2hoaCA5OZkVK1aQlpbGLbfcwiuvvIKIcPXVV3PjjTd2xpyfn8+qVav4/ve/zxtvvMGdd97J9u3b+fzzz8nPz+cXv/gFl156KQ0NDQDce++9fPWrXwXgV7/6FQ8//DBxcXGcdtppXH311Zx//vl88MEHAJSWljJ//nxWr159SL87TyaC+uY2tu1u5OKjR0Y6FGM84+677+aTTz5h7dq1ALzxxhu8//77fPLJJ51dHRctWkRubi6NjY3MnDmT8847j7y8vH2OU1paymOPPcZf//pXLrjgApYuXcoll1zS7fPy8/P54IMPuO+++7jnnnt44IEHuOuuuzjxxBO57bbbWLZsGQsXLgwa689//nNyc3Npb2/npJNO4qOPPmLChAlceOGFPPHEE8ycOZO6ujpSU1NZuHAhW7ZsYc2aNSQkJLBz5879/ixWr17NO++8Q2pqKnv27OG1114jJSWF0tJSLrroIlatWsXLL7/Mc889x3/+8x/S0tLYuXMnubm5ZGdns3btWqZMmcLixYu5/PLLD+wXEYQnE0HHZDRjC22MIeNNvZ2596dZs2bt09/9j3/8I88++ywAW7dupbS0tFsiGD16NFOmTAFg+vTpfP7550GPfe6553bu88wzzwDwzjvvdB5/7ty55OQErxV48sknWbhwIW1tbezYsYN169YhIhQVFTFz5kwAsrKce5CWL1/OtddeS0KCU5zm5ubu93ufeeaZpKamAs6NfjfccANr164lPj6ekpKSzuMuWLCAtLS0fY571VVXsXjxYn7729/yxBNP8P777+/38/bHo4nAxhgyJhqkp+8d3uWNN95g+fLlvPvuu6SlpTFnzpyg/eGTk5M7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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAYgAAAEWCAYAAAB8LwAVAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAAAwwUlEQVR4nO3deXyU9b33/9cnk8keSAJhDbtoRRTEiLT2oHaxuLSuteDSams9XTx3e+6WW23P0S7nd9f72NPtVGuph6qt60GpniMFa6uip26AYMEVkSUsErYQJCHJ5PP747oShjAJSchkkpn38/GYx1xzLTOfhDDv+V7XNdfH3B0REZG2slJdgIiI9E0KCBERSUgBISIiCSkgREQkIQWEiIgkpIAQEZGEFBAiIpKQAkKkG8xsvZl9ItV1iCSTAkJERBJSQIj0EDPLNbOfmdmW8PYzM8sNlw02s/82sz1mtsvMnjOzrHDZDWa22cxqzewtM/t4an8SkUB2qgsQSSPfBWYAUwEHHgP+Cfhn4FtAFVAerjsDcDM7DrgeONXdt5jZWCDSu2WLJKYRhEjPuQL4gbtvd/dq4PvAVeGyRmA4MMbdG939OQ8uhBYDcoFJZhZ19/Xu/m5KqhdpQwEh0nNGABviHm8I5wHcBqwFnjSzdWZ2I4C7rwW+CXwP2G5mD5rZCET6AAWESM/ZAoyJezw6nIe717r7t9x9PPBp4H+3HGtw9/vd/aPhtg78v94tWyQxBYRI90XNLK/lBjwA/JOZlZvZYOBm4PcAZna+mR1jZgbsJdi1FDOz48zsY+HB7HqgLlwmknIKCJHuW0Twht5yywOWAa8BfwNWAP8SrjsReArYB7wA3OHuzxAcf7gV2AFsA4YA3+m1n0CkA6aGQSIikohGECIikpACQkREElJAiIhIQgoIERFJKK0utTF48GAfO3ZsqssQEek3li9fvsPdyxMtS6uAGDt2LMuWLUt1GSIi/YaZbWhvmXYxiYhIQgoIERFJSAEhIiIJpdUxCBHpuxobG6mqqqK+vj7VpWSkvLw8KioqiEajnd5GASEivaKqqori4mLGjh1LcM1C6S3uzs6dO6mqqmLcuHGd3k67mESkV9TX1zNo0CCFQwqYGYMGDery6C1pAWFmo8zsaTN7w8zWmNk3EqxjZvYLM1trZq+Z2bS4ZbPC/rxrW5qriEj/pnBIne787pM5gmgCvuXuxxP03/26mU1qs845BJdBnghcB/wKwMwiwO3h8knAnATb9kyRsWZuf3otS9+uTsbTi4j0W0kLCHff6u4rwula4A1gZJvVLgDu9cCLQImZDQemA2vdfZ27NwAPhuv2uEiWMW/pOhav2ZaMpxeRPmLPnj3ccccd3dr23HPPZc+ePR2uc/PNN/PUU0916/nbGjt2LDt27OiR5zoavXIMwszGAicDL7VZNBLYFPe4KpzX3vxEz32dmS0zs2XV1V0fBZgZE8oLeXf7vi5vKyL9R0cBEYt13MRv0aJFlJSUdLjOD37wAz7xiU90t7w+KekBYWZFwCPAN919b9vFCTbxDuYfPtN9nrtXuntleXnCy4kc0TFDini3WgEhks5uvPFG3n33XaZOncrcuXN55plnOOuss7j88ss58cQTAbjwwgs55ZRTOOGEE5g3b17rti2f6NevX8/xxx/Pl7/8ZU444QTOPvts6urqALj66qtZsGBB6/q33HIL06ZN48QTT+TNN98EoLq6mk9+8pNMmzaNv//7v2fMmDFHHCn85Cc/YfLkyUyePJmf/exnAHzwwQecd955TJkyhcmTJ/PQQw+1/oyTJk3ipJNO4tvf/vZR/86SepqrmUUJwuE+d380wSpVwKi4xxUETd5z2pmfFBPKi3h4WRV79jdQUpCTrJcRkdD3/2sNr29p+3nx6EwaMYBbPn1Cu8tvvfVWVq9ezcqVKwF45plnePnll1m9enXrqZ/z58+nrKyMuro6Tj31VC655BIGDRp0yPO88847PPDAA/zmN7/hsssu45FHHuHKK6887PUGDx7MihUruOOOO/jxj3/MXXfdxfe//30+9rGPcdNNN7F48eJDQiiR5cuX89vf/paXXnoJd+e0007jjDPOYN26dYwYMYInnngCgJqaGnbt2sXChQt58803MbMj7hLrjGSexWTAfwBvuPtP2lntceDz4dlMM4Aad98KvAJMNLNxZpYDzA7XTYoJ5UUAvFv9QbJeQkT6oOnTpx/yvYBf/OIXTJkyhRkzZrBp0ybeeeedw7YZN24cU6dOBeCUU05h/fr1CZ/74osvPmyd559/ntmzZwMwa9YsSktLO6zv+eef56KLLqKwsJCioiIuvvhinnvuOU488USeeuopbrjhBp577jkGDhzIgAEDyMvL49prr+XRRx+loKCgi7+N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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "#plotting graph for accuracy \n",
    "plt.figure(0)\n",
    "plt.plot(history.history['accuracy'], label='training accuracy')\n",
    "plt.plot(history.history['val_accuracy'], label='val accuracy')\n",
    "plt.title('Accuracy')\n",
    "plt.xlabel('epochs')\n",
    "plt.ylabel('accuracy')\n",
    "plt.legend()\n",
    "plt.show()\n",
    "\n",
    "#plotting graph for loss\n",
    "plt.figure(1)\n",
    "plt.plot(history.history['loss'], label='training loss')\n",
    "plt.plot(history.history['val_loss'], label='val loss')\n",
    "plt.title('Loss')\n",
    "plt.xlabel('epochs')\n",
    "plt.ylabel('loss')\n",
    "plt.legend()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "id": "0b88309f",
   "metadata": {},
   "outputs": [],
   "source": [
    "#Testing on test data\n",
    "from sklearn.metrics import accuracy_score\n",
    "\n",
    "y_test = pd.read_csv('Test.csv')\n",
    "\n",
    "labels = y_test[\"ClassId\"].values\n",
    "imgs = y_test[\"Path\"].values\n",
    "\n",
    "data=[]\n",
    "\n",
    "for img in imgs:\n",
    "    image = Image.open(img)\n",
    "    image = image.resize((30,30))\n",
    "    data.append(np.array(image))\n",
    "\n",
    "X_test=np.array(data)\n",
    "\n",
    "pred = np.argmax(model.predict(X_test), axis=-1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "id": "3e4773d4",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "0.9555819477434679\n"
     ]
    }
   ],
   "source": [
    "#Checking accuracy\n",
    "from sklearn.metrics import accuracy_score\n",
    "print(accuracy_score(labels, pred))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "id": "205ea6f5",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Classes of trafic signs\n",
    "classes = { 0:'Speed limit (20km/h)',\n",
    "            1:'Speed limit (30km/h)', \n",
    "            2:'Speed limit (50km/h)', \n",
    "            3:'Speed limit (60km/h)', \n",
    "            4:'Speed limit (70km/h)', \n",
    "            5:'Speed limit (80km/h)', \n",
    "            6:'End of speed limit (80km/h)', \n",
    "            7:'Speed limit (100km/h)', \n",
    "            8:'Speed limit (120km/h)', \n",
    "            9:'No passing', \n",
    "            10:'No passing veh over 3.5 tons', \n",
    "            11:'Right-of-way at intersection', \n",
    "            12:'Priority road', \n",
    "            13:'Yield', \n",
    "            14:'Stop', \n",
    "            15:'No vehicles', \n",
    "            16:'Veh > 3.5 tons prohibited', \n",
    "            17:'No entry', \n",
    "            18:'General caution', \n",
    "            19:'Dangerous curve left', \n",
    "            20:'Dangerous curve right', \n",
    "            21:'Double curve', \n",
    "            22:'Bumpy road', \n",
    "            23:'Slippery road', \n",
    "            24:'Road narrows on the right', \n",
    "            25:'Road work', \n",
    "            26:'Traffic signals', \n",
    "            27:'Pedestrians', \n",
    "            28:'Children crossing', \n",
    "            29:'Bicycles crossing', \n",
    "            30:'Beware of ice/snow',\n",
    "            31:'Wild animals crossing', \n",
    "            32:'End speed + passing limits', \n",
    "            33:'Turn right ahead', \n",
    "            34:'Turn left ahead', \n",
    "            35:'Ahead only', \n",
    "            36:'Go straight or right', \n",
    "            37:'Go straight or left', \n",
    "            38:'Keep right', \n",
    "            39:'Keep left', \n",
    "            40:'Roundabout mandatory', \n",
    "            41:'End of no passing', \n",
    "            42:'End no passing veh > 3.5 tons' }"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "id": "e9215f6a",
   "metadata": {},
   "outputs": [],
   "source": [
    "#making predictions\n",
    "from PIL import Image\n",
    "import numpy as np\n",
    "import matplotlib.pyplot as plt\n",
    "def test_on_img(img):\n",
    "    data=[]\n",
    "    image = Image.open(img)\n",
    "    image = image.resize((30,30))\n",
    "    data.append(np.array(image))\n",
    "    X_test=np.array(data)\n",
    "    pred = np.argmax(model.predict(X_test), axis=-1)\n",
    "    return image,pred"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "id": "56db9754",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Predicted traffic sign is:  Speed limit (50km/h)\n"
     ]
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "#sample image 00034.png\n",
    "plot,prediction = test_on_img(r'D:\\Traffic_Sign_Recognition\\Test\\00034.png')\n",
    "s = [str(i) for i in prediction] \n",
    "a = int(\"\".join(s)) \n",
    "print(\"Predicted traffic sign is: \", classes[a])\n",
    "plt.imshow(plot)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "id": "0fcfb590",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Predicted traffic sign is:  Stop\n"
     ]
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "#sample image 00093.png\n",
    "plot,prediction = test_on_img(r'D:\\Traffic_Sign_Recognition\\Test\\00093.png')\n",
    "s = [str(i) for i in prediction] \n",
    "a = int(\"\".join(s)) \n",
    "print(\"Predicted traffic sign is: \", classes[a])\n",
    "plt.imshow(plot)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "id": "7774f177",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Predicted traffic sign is:  Turn right ahead\n"
     ]
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "#sample image 00480.png\n",
    "plot,prediction = test_on_img(r'D:\\Traffic_Sign_Recognition\\Test\\00480.png')\n",
    "s = [str(i) for i in prediction] \n",
    "a = int(\"\".join(s)) \n",
    "print(\"Predicted traffic sign is: \", classes[a])\n",
    "plt.imshow(plot)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "id": "49aff93c",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Predicted traffic sign is:  Bicycles crossing\n"
     ]
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "#sample image 00597.png\n",
    "plot,prediction = test_on_img(r'D:\\Traffic_Sign_Recognition\\Test\\00597.png')\n",
    "s = [str(i) for i in prediction] \n",
    "a = int(\"\".join(s)) \n",
    "print(\"Predicted traffic sign is: \", classes[a])\n",
    "plt.imshow(plot)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 22,
   "id": "dac41bb5",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Predicted traffic sign is:  Road work\n"
     ]
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "#sample image 00868.png\n",
    "plot,prediction = test_on_img(r'D:\\Traffic_Sign_Recognition\\Test\\00868.png')\n",
    "s = [str(i) for i in prediction] \n",
    "a = int(\"\".join(s)) \n",
    "print(\"Predicted traffic sign is: \", classes[a])\n",
    "plt.imshow(plot)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "2feff57b",
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.8.8"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
