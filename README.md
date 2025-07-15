

# Lab 3 - Continuous Deploy - Frontend

### Summary: Extend your existing pipeline to take the artifact built in the CI/Build stage and deploy it to an environment

**Learning Objective(s):**

- Add a second stage to an existing pipeline

- Create a k8s service

- Incorporate an advanced deployment strategy such as Canary

- Create custom Harness variables

- Create an Input Set

**Steps**

2. In the existing pipeline, add a Deployment stage by clicking **Add Stage** and select **Deploy** as the Stage Type

3. Enter the following values and click on **Set Up Stage**


| Input           | Value          | Notes |
| --------------- | -------------- | ----- |
| Stage Name      |frontend|       |
| Deployment Type |Kubernetes|       |

4. Configure the **frontend** Stage with the following\
   **Service**

- Click **+Add Service** and configure as follows****


| Input                      | Value                                               | Notes                              |
| -------------------------- | --------------------------------------------------- | ---------------------------------- |
| Name                       |frontend|                                    |
| Deployment Type            |Kubernetes|                                    |
| * **Add Manifest**         |                                                     |                                    |
| Manifest Type              |K8s Manifest|                                    |
| K8s Manifest Store         |Code|                                    |
| Manifest Identifier        |templates|                                    |
| Repository                 |harnessrepo|                                    |
| Branch                     |main|                                    |
| File/Folder Path           |harness-deploy/frontend/manifests|                                    |
| Values.yaml                |harness-deploy/frontend/values.yaml|                                    |
| - **Add Artifact Source**  |                                                     |                                    |
| Artifact Repository Type   |Docker Registry|                                    |
| Docker Registry Connector  |dockerhub|                                    |
| Artifact Source Identifier |frontend|                                    |
| Image Path                 |nikpap/harness-workshop|                                    |
| Tag                        |<+variable.username>-<+pipeline.sequenceId>| _Select value, then click on the pin and select expression and paste the value |

**Environment**

The target infrastructure has been pre-created for us. The application will be deployed to a k8s cluster on the given namespace  

- Click **- Select -** on the environment input box ****

- Select **prod** environment****

| Input | Value | Notes                                                             |
| ----- | ----- | ----------------------------------------------------------------- |
| Name  |prod| Make sure to select the environment and infrastructure definition |

- Click **- Select -** on the environment input box ****

-  From the dropdown select k8s



| Input | Value | Notes |
| ----- | ----- | ----- |
| Name  |k8s|       |

**Execution**

- Select **Rolling** and click on **Use Strategy**, the frontend is a static application so no need to do canary, new features will be managed by Feature Flags at a later stage of this lab


# Lab 4 - Continuous Deploy - Backend

### Summary: Extend your existing pipeline to derisk production deployments

**Learning Objective(s):**

- Utilise complex deployment strategies to reduce blast radius of a release 

**Steps**

5. In the existing pipeline, add a Deployment stage by clicking **Add Stage** and select **Deploy** as the Stage Type

6. Enter the following values and click on **Set Up Stage**


| Input           | Value          | Notes |
| --------------- | -------------- | ----- |
| Stage Name      |backend|       |
| Deployment Type |Kubernetes|       |

7. Configure the **backend** Stage with the following\
   **Service**

- Click **Select Service** and configure as follows****


| Input | Value       | Notes |
| ----- | ----------- | ----- |
| Name  |backend|       |

**Environment**

The target infrastructure has been pre-created for us and we used it in the previous stage. To reuse the same environment

- Click **- Propagate Environment From**

- Select **Stage \[frontend]**

**Execution**

- Select **Canary**  and click on **Use Strategy**

- **After** the canary deployment and **before** the canary delete step add **Harness Approval** step according to the table  below

| Input            | Value            | Notes |
| ---------------- | ---------------- | ----- |
| Step Name        |Approval|       |
| Type of Approval |Harness Approval|       |

- Configure the Approval step as follows

| Input       | Value             | Notes |
| ----------- | ----------------- | ----- |
| Name        |Approval|       |
| User Groups |All Project Users|       |


8. Click **Save** and then click **Run** to execute the pipeline with the following inputs. As a bonus, save your inputs as an Input Set before executing (see below)

| Input       | Value | Notes       |
| ----------- | ----- | ----------- |
| Branch Name |main| Leave as is |

9. While the canary deployment is ongoing and waiting **approval** navigate to the web page and see if you can spot the canary (use the check release button) 

| project                | domain        | suffix |
| ---------------------- | ------------- | ------ |
|http\://project_id|.cie-bootcamp|.co.uk|

![](https://lh7-us.googleusercontent.com/docsz/AD_4nXfmb1N3lAe0EOnEun9neU9y3ilqy3HbxfnWfUMzF3FsykslwgQfU_W4pE0wlt5kYSp6_mTs7cVP0anhJ7uvtsytal2qX3ZEq3vvOT3DOBUzE9SZ3rpwkAHP6e_ExdRbo5VmN2kpxdFlp6u8iGaKwhW_uyAohEmJurkjmEB2Ww?key=cRG2cvp_PHVW0KG2Gq6Y_A)

10. Approve the canary deployment for the pipeline to complete

# Lab 5 - Feature Flags

### Summary: Build and deploy your first feature flag 

**Learning Objective(s):**

- Create a Feature Flag

- Create an SDK key

- Deploy application that uses Flag/SDK Key

- Toggle Feature Flag to enable/disable feature

**Steps**

![](https://lh7-us.googleusercontent.com/docsz/AD_4nXdxbh_5hgTG2CsE8Dp_5_BLB75OITfS-9xxW-xplPehdYbj38WMTloCOo4tbOAom9VRc65S99IB54w-TY7INiG6Bd8PMqvRs_EsTQHzKjCZTjnv8laP7XCEuf9_l3s8HV3UuxVsnTgzuZpkV6Fq-FVoqpHY5kSuQ3un7Xrssg?key=cRG2cvp_PHVW0KG2Gq6Y_A)

**Create the SDK Key**  

1. From the left hand side menu under Feature Flags,  select **environments**

2. From the list select the prod environment

3. Click **+ New SDK Key**, configure as follows and click **Create**

| Input    | Value      | Notes |
| -------- | ------     | ----- |
| Name     |sdk|       |
| Key Type |client|       |

4. Copy the secret to use later. Note that the key will be redacted once you leave the page.

5. From the left hand side menu select Project settings

6. From the resources available click on the **Variables** 

7. Modify the sdk variable and copy in the key

| Input | Value                               | Notes |
| ----- | ----------------------------------- | ----- |
| Name  |sdk|       |
| Value | _SDK Key copied from previous step_ |       |

4. Click **Save**

********

**Create the Flag**

1. From the left hand menu, go to **Feature Flags** → **Feature Flags**

2. Click **+ New Feature Flag,** configure as follows and click **Save and Close**.

| Input                         | Value      | Notes |
| ----------------------------- | --------------   | ----- |
| Type                          |Boolean|       |
| Name                          |webinarff|       |
| **Variation Settings**        |                  |       |
| Name (first one)              |Show Offer|       |
| Name (second one)             |Hide Offer|       |
| If the flag is Enabled, serve |Show Offer|       |

3. Enable the flag by clicking on the **Flag is Disabled** button and click **Save**


5. **Run** the pipeline created in previous steps

6. **Approve canary deployment** before progressing to the next step

**Change the Flag via the UI**

1. From the left hand menu in Harness, go to **Feature Flags** → **Target Management**

2. Select the target shown in the list. If target is not shown, create the target manually

| Input      | Value     | Notes |
| ---------- | --------- | ----- |
| Name       |webinar|       |
| Identifier |webinar|       |

3. Click **Add Flag**, toggle **webinarff**, set the variation to **Show Offer**, then click on **Add 1 Flags**

4. Note that your application now displays a special offer

5. For your target, set the variation to **Hide Offer** and click **Save Chances**

6. Note that your application now does NOT display the special offer

# Lab 6 - Continuous Verification


### Summary: Automate the verification of new releases 

**Learning Objective(s):**

- Add continuous verification to the deployed service

- Automate release validation

**Steps**

1. In the existing pipeline, within the Deploy backend stage **after** Canary Deployment and **before** the approval step click on the plus icon to add a new step

2. Add a **Verify** step with the following configuration

| Input                        | Value  | Notes                                                                                            |
| ---------------------------- | ------ | ------------------------------------------------------------------------------------------------ |
| Name                         |Verify|                                                                                                  |
| Continuous Verification Type |Canary|                                                                                                  |
| Sensitivity                  |Low| This is to define how sensitive the ML algorithms are going to be on deviation from the baseline |
| Duration                     |5mins|                                                                                                  |

Click **Save** and then click **Run** to execute the pipeline with the following inputs. As a bonus, save your inputs as an Input Set before executing (see below)

| Input       | Value | Notes       |
| ----------- | ----- | ----------- |
| Branch Name |main| Leave as is |


# Lab 7 - Validate Release

**Learning Objective(s):**

- Identify the difference in traffic between normal and canary instances of the application

- Automate release validation

- Use complex deployment strategies to reduce the blast radius

**Steps**:

- While the canary deployment is ongoing navigate to the web page and see if you can spot the canary (use the check release button) 

| project                | domain        | suffix |
| ---------------------- | ------------- | ------ |
| http\://\<project\_id>|.cie-bootcamp|.co.uk|

- Drill down to the distribution test tab and run the traffic generation by clicking the **Play** button

- Observe the traffic distribution

- Validate the outcome of the verification on the pipeline execution details

\
![](https://lh7-us.googleusercontent.com/docsz/AD_4nXdbAmEJ5zQPsKlw_nEknWvYo97pm5eWCXr6vU8-GgIL0ulAOSH9N07PoEcVSknARVQo7Tgj1s31VHqR1I3hu2dMIO1rIX5HHcmTPXoQPoyo8CPv13OhnJN5WVcZqSwUXzdDHmm3PxUnhtpGVl0PAMJ_1wnuodvUbVPBOdnGKQ?key=cRG2cvp_PHVW0KG2Gq6Y_A)
![](https://lh7-us.googleusercontent.com/docsz/AD_4nXf-5oWX9OfvdmEb9MBm2_h2KKAa_QwmiJoM0fiKrTuxAr6GR4wxeulSlk48gyBK3dykrtIslDSkxpiGytrxH0JaxaQ4ZgTYxbmc8OenAH3nhGCvvOAxkWVjVBp1TRg_qQQi9z8OrNPK4udPtNL1LIyym6Ch5IMzrulFOcXhOQ?key=cRG2cvp_PHVW0KG2Gq6Y_A)

**Bonus**:

- Add a canary rollout from 10% to 50% traffic and see how this impacts the traffic distribution


# Lab 8 - Governance/Policy as Code

### Summary: Create and apply policies as code in order to enable governance and promote self-service. In Lab 2 we saw how a user is impacted by policies in place, now is the time to create such policies

**Learning Objective(s):**

- Create a policy that evaluates when editing pipelines

- Create a policy that evaluates during pipeline execution

- Test policy enforcement

**Steps**
**Create a Policy to require Approvals**

1. From the secondary menu, select **Project Settings** and select **Governance Policies**

2. Click **Build a Sample Policy**

3. From the suggested list select **Pipeline - Approval**  and click on next

4. Click Next: Enforce Policy

5. Set the values according to the table  below and confirm

| Input            | Value        | Notes |
| ---------------- | ------------ | ----- |
| Trigger Event    |On Run|       |
| Failure Strategy |Error & exit|       |

**Test the Policy to require Approvals**

1. Open your pipeline

2. Try to run the pipeline and note that the failure due to lack of an approval stage

3. Open the pipeline in edit mode and navigate to the “**frontend**” stage

4. Before the rolling deployment step add **Harness Approval** step according to the table  below

| Input            | Value            | Notes |
| ---------------- | ---------------- | ----- |
| Step Name        |Approval|       |
| Type of Approval |Harness Approval|       |

5. Configure the Approval step as follows

| Input       | Value             | Notes |
| ----------- | ----------------- | ----- |
| Name        |Approval|       |
| User Groups |All Project Users|       |

6. Navigate to the “**backend**” stage
7. Repeat steps 4-5 to add an approval **before** the canary deployment block 
8. Click **Save** and note that the save succeeds without any policy failure


# Lab 9 - Governance/Policy as Code (Advanced)

**Create a Policy to block critical CVEs**

1. From the secondary menu, select **Project Settings** and select **Policies**

2. Select the **Policies** tab 

3. click **+ New Policy**, set the name to **Runtime OWASP CVEs** and click **Apply**

4. Set the rego to the following and click **Save**

<!---->

    package pipeline_environment
    deny[sprintf("Node OSS Can't contain any critical vulnerability '%d'", [input.NODE_OSS_CRITICAL_COUNT])] {  
       input.NODE_OSS_CRITICAL_COUNT != 0
    }

5. Select the **Policy Sets** tab

6. Click **+ New Policy Set** and configure as follows

| Input                      | Value                     | Notes |
| -------------------------- | ------------------------- | ----- |
| Name                       |Criticals Not Allowed|       |
| Entity Type                |Custom|       |
| Event Evaluation           |On Step|       |
| Policy Evaluation Criteria |                           |       |
| Policy to Evaluate         |Runtime OWASP CVEs|       |

7. For the new policy set, toggle the **Enforced** button

**Add Policy to Pipeline**

1. Open your pipeline

2. Go to an execution that already ran, and copy the CRITICAL output variable from the OWASP step like so:\
   ![](https://lh7-us.googleusercontent.com/docsz/AD_4nXfYQ7ba5Q_cQ9xy2AFVZ5Mt0iZPYbyQDmBonp0pBQA13Z_IUeYdK8gRSbddtf_V3bSRfbhKWDbRSUVJTx3BTCc_VmwLIWyWLkdh89nLh0sEBA6fqQxTy0NADZ0YPZwCirNycRVGUQACdItaBotovPs5Hg6CmRpQHk5ysgV6RUlhSbIbkNxmHAo?key=cRG2cvp_PHVW0KG2Gq6Y_A)

3. Select the **frontend** stage

4. Before the **Rollout Deployment** Step Group, add a **Policy** type step and configure as follow

| Input       | Value                                          | Notes                                                                                                                                                   |
| ----------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name        |Policy - No Critical CVEs|                                                                                                                                                         |
| Entity Type |Custom|                                                                                                                                                         |
| Policy Set  |Criticals Not Allowed| Make sure to select the Project tab in order to see your Policy Set                                                                                     |
| Payload     |{"NODE\_OSS\_CRITICAL\_COUNT": _\<variable>_}| Set the field type to Expression, then replace _\<variable>_ with OWASP output variable CRITICAL. Go to a previous execution to copy the variable path. |

5. Save the pipeline and execute. Note that the pipeline fails at the policy evaluation step due to critical vulnerabilities being found by OWASP.
