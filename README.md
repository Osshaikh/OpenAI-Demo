# Load Balance request across Multiple OpenAI deployment models via L7 LB App gateway

Demand for OpenAI Service on Azure has been increasing exponentially since its announcement.

One of common problems face by some customers it's exhaustion of their (TPM) i.e., 240K Token limits for each instance in region, similar to other services even Az OpenAI has limits to reduce service abuse and meets scalability requirements.

One way to fix this problem is deploy multiple Az OpenAI LLM models across different region and use Load balancer in front,but there is one more complexity with this solution, since each OpenAI LLM deployments can be access using thier individual Endpoint and Key, in that case its diffiult to round robin via ALB since your request for OpenAI contains key for specific instance it wont work when request round robin to different instance in LB backend pool

There are some complex solution to deal this problem involving using APIM/Appgw, Az Function and Web-app like this: https://lnkd.in/dSmyDtzY

An Easier approach for this solution is to use Service Principal with Az RBAC for OpenAI & with LB like App Gateway in this sequence.
* Create Service principal in Azure AD 
* Assign Permission on OpenAI RG/Subscription That consist of OpenAI resources in across region using RBAC "Open AI User/Contributor."
* Configure all OpenAI endpoints in backend pools & its relevant health probes
* Configue your OpenAI DNS name that would bind Appgw frontend endpoint
* Use service principal in your App or RestAPI fetching secrets on the fly via keyvault

Since SPN will have permission across on OpenAI instances, request will get authorized via SPN bearer token and received response of prompt from one of OpenAI models.

Following are some screenshots of Demo i did for one of customers

Sample post request to Appgw Public Endpoint via Postman
![image](https://github.com/Osshaikh/OpenAI-Demo/assets/44756471/f95e337c-aabb-41b1-a0f9-adbee48ea780)

Header and Authentication details of request Load balance across multiple OpenAI models located in EastUS & UKSouth via LB AppGW
![image](https://github.com/Osshaikh/OpenAI-Demo/assets/44756471/5d6a057a-0a25-4f2e-88be-bd7b2014cb9c)

![image](https://github.com/Osshaikh/OpenAI-Demo/assets/44756471/2aee1332-9ef0-464a-9ca3-3c628f32347f)

To configure Session stickness for OpenAI models 
![image](https://github.com/Osshaikh/OpenAI-Demo/assets/44756471/6ccffaf2-c22a-4c96-9d02-bb7f7395a1d0)

Use following settings in configuring health probes for your OpenAI models
![image](https://github.com/Osshaikh/OpenAI-Demo/assets/44756471/44f75b78-d811-428e-a16d-084daa72fc04)

BackendPool Details that consist Both OpenAI Models
![image](https://github.com/Osshaikh/OpenAI-Demo/assets/44756471/d0cf146f-b65a-408c-8f45-397420f709f1)

Application Gateway Public Endpoint
![image](https://github.com/Osshaikh/OpenAI-Demo/assets/44756471/0c93f3f2-5821-4b9c-812e-fbab18db1c24)


