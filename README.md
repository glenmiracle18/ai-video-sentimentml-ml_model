![alt text](thumbnail.png)

[https://youtu.be/Myo5kizoSk0](https://youtu.be/Myo5kizoSk0)

## Overview

Hi 🤙 In this video, you'll learn how to build a multimodal AI model using PyTorch. The model will accept a video as its input, and predict its sentiment and emotion. When training the model, you'll build features like text, video, and audio encoding, multimodal fusion, and emotion and sentiment classification. After training and deploying the model, you'll build a SaaS around your trained model, where users can run inference on their videos through your API. You'll set up invocation of the deployed model with SageMaker Endpoints, and manage the monthly quotas users have. The SaaS will be built with technologies such as Next.js, React, Tailwind, and Auth.js and is based off of the T3 Stack. You'll be able to build along with me from start to finish.

Features:

- 🎥 Video sentiment analysis
- 📺 Video frame extraction
- 🎙️ Audio feature extraction
- 📝 Text embedding with BERT
- 🔗 Multimodal fusion
- 📊 Emotion and sentiment classification
- 🚀 Model training and evaluation
- 📈 TensorBoard logging
- 🚀 AWS S3 for video storage
- 🤖 AWS SageMaker endpoint integration
- 🔐 User authentication with Auth.js
- 🔑 API key management
- 📊 Usage quota tracking
- 📈 Real-time analysis results
- 🎨 Modern UI with Tailwind CSS

## Setup

Follow these steps to install and set up the project.

### Clone the Repository

```bash
git clone https://github.com/Andreaswt/ai-video-sentiment-model.git
```

### Navigate to the Project Directory

```bash
cd ai-video-sentiment-model
```

### Install Python

Download and install Python if not already installed. Use the link below for guidance on installation:
[Python Download](https://www.python.org/downloads/)

### Install Dependencies

```bash
pip install -r training/requirements.txt
```

### Download the Dataset

Visit the following link to download the MELD dataset:
[MELD Dataset](https://affective-meld.github.io)

Extract the dataset and place it in the `dataset` directory.

PS: learn more about state-of-the-art model in the following [Emotion Recognition Benchmark for the MELD dataset ](https://paperswithcode.com/sota/emotion-recognition-in-conversation-on-meld).

### Start Training Job

Follow these steps to train the model in a training job using AWS SageMaker:

1. Request a quota increase for an instance for training job usage for SageMaker - e.g. ml.g5.xlarge

2. Put the dataset in an S3 bucket

3. Create a role with Policies

- AmazonSageMakerFullAccess
- Access to S3 bucket with dataset

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "VisualEditor0",
			"Effect": "Allow",
			"Action": [
				"s3:PutObject",
				"s3:GetObject",
				"s3:ListBucket",
				"s3:DeleteObject"
			],
			"Resource": [
				"arn:aws:s3:::your-bucket-name",
				"arn:aws:s3:::your-bucket-name/*"
			]
		}
	]
}
```

4. Run the file locally, to start the training job.

```bash
python train_sagemaker.py
```

### Deploy Endpoint

Follow these steps to deploy the model as an endpoint using AWS SageMaker:

1. Create a deployment role in AWS with permissions

- AmazonSageMakerFullAccess
- CloudWatchLogsFullAccess

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "VisualEditor0",
			"Effect": "Allow",
			"Action": [
				"s3:PutObject",
				"s3:GetObject",
				"s3:ListBucket",
				"s3:DeleteObject"
			],
			"Resource": [
				"arn:aws:s3:::your-bucket-name",
				"arn:aws:s3:::your-bucket-name/*"
			]
		}
	]
}
```

2. Put your model file in an S3 bucket

3. Deploy the endpoint by runnin the file locally:

```bash
python deployment/deploy_endpoint.py
```

### Invoke Endpoint

1. Create a user in IAM with permissions

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::sentiment-analysis-saas/inference/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "sagemaker:InvokeEndpoint"
            ],
            "Resource": [
                "arn:aws:sagemaker:us-east-1:784061079855:endpoint/sentiment-analysis-endpoint"
            ]
        }
    ]
}
```

2. Use the user to invoke endpoint. E.g. use [this NPM library](https://www.npmjs.com/package/@aws-sdk/client-sagemaker-runtime) for invoking from JavaScript:

### Access TensorBoard

1. Download logs to local machine:
   `aws s3 sync s3://your-bucket-name/tensorboard ./tensorboard_logs`

2. Start tensorboard server
   `tensorboard --logdir tensorboard_logs`

3. Open your browser and visit:
   [http://localhost:6006](http://localhost:6006)

