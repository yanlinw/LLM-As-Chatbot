# Secure Your Private LLM and MLops with Splashtop Secure Workspace

This repository is a fork of __dstackai/LLM-As-Chatbot__. __dstack__ is an open-source tool that streamlines machine learning workload in the cloud as if it is running locally. The original __LLM-As-Chatbot__ open-source project allows people to run multiple fine-tuned open-source LLM models as a Chatbot service. This project allows you to run LLM-As-Chatbot in your cloud with a single dstack run command and also provisions the Splashtop Secure Workspace connector to connect your private LLM chatbot to your Secure Workspace network. This enables you to control who in your organization can access this private LLM service, specifying conditions such as time, location, company device, client security posture, and more.

## 1. Clone the repository

```bash
git clone https://github.com/yanlinw/LLM-As-Chatbot.git
cd LLM-As-Chatbot
```

## 2. Install and set up dstack

```bash
pip install "dstack[aws,gcp,azure,lambda]" -U
dstack start
```

This will start the dstack server. Make sure to log in and create a project with your cloud credentials (AWS, GCP, or Azure). After creating the project, copy its dstack config command and run it locally to configure the CLI to use this project.

## 3. Create a dstack profile

Create a `.dstack/profiles.yml` file under the root of the LLM-As-Chatbot folder. This file should point to the created project and describe the resources.

Example:

```yaml
profiles:
  - name: aws-llm
    project: aws-llm
    resources:
      memory: 16GB
      gpu:
        count: 1
    spot_policy: auto    
    default: True 
```

## 4. Initialization

Run the following `dstack init` command:

```bash
dstack init
```

## 5. Setup Secure Workspace for your private LLM

Splashtop Secure Workspace Connectors provide a secure solution for connecting your private applications and resources to the Splashtop Secure Workspace. This allows you to centrally control these private applications. We will now connect the private LLM we are working on to the Secure Workspace.

### 5.1 Create connector and copy the connector token

Log in to your Splashtop Secure Workspace admin account using your credentials. Once logged in, navigate to the **Deployment** menu and select **Connector**. Locate the **Add Connector** button and click on it. Choose **Headless / CLI**, fill in the connector name, and click **Next**. Choose **Linux** and click **Done**.

After creating the connector object, you can click on the connector name from the connectors list to show the connector details. Copy the **Token** as you will need it in step 6.

### 5.2 Create the LLM application

We will add the private LLM chatbot service as a private application in the Secure Workspace so you can provision this private LLM application to your employees.

Log in to your Splashtop Secure Workspace admin account using your credentials. Navigate to the **Applications/Applications** section and click the **Add Application/Add Private Application** button. In the application form, fill in the application name, set 'localhost' as the **Host**, '6006' as the **Port**, choose **HTTP** as the **Protocol**, choose the connector name you created in the previous step, assign the proper group to this application, and click **Save**.



The Secure Workspace will generate a fully qualified domain name for the private LLM application you just created.

## 6. Run the app in your cloud

In the LLM-As-Chatbot folder, use the `dstack run` command to provision the private LLM and Secure Workspace in your cloud (replace `$token` with the connector token you copied from step 5.1):

```bash
dstack run . -f ssw-private-llm.yml $token 
```

This command will build the environment and run LLM-As-Chatbot in your cloud. It will also start the Secure Workspace connector instance along with your private LLM.

Once the private LLM service is fully started, you can use the fully qualified domain name for the private LLM application you created to access the private LLM. Meanwhile, you can set up entitlement and conditional access policies for this application.

![Private LLM Chatbot](/assets/guimode_preview.gif)