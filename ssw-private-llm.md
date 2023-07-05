
# Secure Private LLM in Your Cloud

This repository is a fork of dstackai/LLM-As-Chatbot. dstack is an open-source tool that streamlines machine learning workloads in the cloud as if they are running locally. The original LLM-As-Chatbot open-source project allows people to run multiple fine-tuned open-source LLM models as a Chatbot service. This project allows you to run LLM-As-Chatbot in your cloud with a single dstack run command and also provisions the Splashtop Secure Workspace connector to connect your private LLM chatbot to your Secure Workspace network. This way, you can control who in your organization can access this private LLM service based on conditions such as time, location, company device, client security posture, etc.

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

Create a `.dstack/profiles.yml` file under the root of the LLM-As-Chatbot folder, which points to the created project and describes the resources.

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

Run the `dstack init` command:

```bash
dstack init
```

## 5. Setup Secure Workspace for your private LLM

Splashtop Secure Workspace Connectors provide a secure solution for connecting your private applications and resources to the Splashtop Secure Workspace, allowing you to centrally control these private applications. We will need to connect the private LLM we are working on to the Secure Workspace.

### 5.1 Create Connector and copy the connector token

Log in to your Splashtop Secure Workspace admin account using your credentials. Once logged in, navigate to the **Deployment** menu and select **Connector**, locate the **Add Connector** button, and click it. Choose **Headless / CLI**, fill in the connector name, then click **Next**, choose **Linux**, and click **Done**.

After creating the connector object, you can click on the connector name from the connectors list to show the connector details. Copy the **Token**. You will need to use this connector token in step 6.

### 5.2 Create the LLM application

We will add the private LLM chatbot service as a private application in the Secure Workspace so you can provision this private LLM application to the employees.

Log in to your Splashtop Secure Workspace admin account using your credentials, navigate to the **Applications/Applications** section, click the **Add Application** or **Add Private Application** button. In the application form, fill in the application name, enter 'localhost' in **Host**, enter '6006' in **Port**, choose **HTTP** as **Protocol**, choose the connector name you just created in the previous step, assign the proper group

to this application, and click **Save**.

Secure Workspace will generate a fully qualified domain name for the private LLM application you just created.

## 6. Run the app in your cloud

In the LLM-As-Chatbot folder, use the `dstack run` command to provision the private LLM as well as Secure Workspace to your cloud (replace the `$token` with the connector token you copied from step 5.1):

```bash
dstack run . -f ssw-private-llm.yml $token 
```

This command will build the environment and run LLM-As-Chatbot in your cloud. It will also start the Secure Workspace connector instance along with your private LLM.

After the private LLM service is fully started, you can use the fully qualified domain name for the private LLM application you just created to access the private LLM. Meanwhile, you can set up entitlement and conditional access policies for this application.

![Private LLM Chatbot](/assets/guimode_preview.gif)

