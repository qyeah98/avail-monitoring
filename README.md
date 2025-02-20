# avail-monitoring
Avail is a modular blockchain with a data availability layer designed to scale blockchain networks.

---

# 1. Set up monitoring system: Prometheus + Grafana
> [!TIP]
> **_Purpose_**  
> The purpose of system monitoring is to detect potential issues or anomalies in the system, so that they can be addressed proactively before they cause significant problems on network.
> 
> **_Prometheus_**  
> An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting approach.
>   
> **_Grafana_**  
> The open and composable observability and data visualization platform. Visualize metrics, logs, and traces from multiple sources like Prometheus and Loki.

Please follow [avail official guide](https://docs.availproject.org/docs/operate-a-node/become-a-validator/monitor-your-validator).  
Download the [Avail grafana json file](https://github.com/qyeah98/avail-monitoring/blob/main/grafana_avail_validator.json) and upload it to grafana dashboard.

---

# 2. Avail metrics
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/a0edb492-33e0-4ee6-a351-407fe94bda67" />
There are the most important metrics you should monitor and integrate notification system.  

### - Block Height
`substrate_block_height`
- `best`: This is the most recent block that the node considers to be the best known block.
- `finalised`: This is the last block that has been finalised and confirmed as a permanent part of the chain.
- `sync_target (sync target)`: This is the block height target that the node is trying to reach during synchronisation.

> [!CAUTION]
> If Best Sync LAG /  Finalized  Sync LAG is increasing, you node can NOT catch up the latest block.
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/3f960962-2c1b-4ed1-884b-88751b86f626" />

### - Active Sync Peers
`substrate_sync_peers`  
Number of active peers with which the node is currently synchronizing.  
This metric specifically measures the peers participating in the block synchronization process.  

> [!CAUTION]
> If the metrics is 0, sync lag might be occured.
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/540a5ebb-2009-45bd-91ce-a82cd122df5a" />

### - Average Block Proposal Time
`substrate_proposer_block_proposal_time_sum`  
`substrate_proposer_block_proposal_time_count`  
This metric measures the time it takes to build a block and prepare it for the proposal.   

### - Average Block Construction Time
`substrate_proposer_block_constructed_sum`  
`substrate_proposer_block_constructed_count`  
Time it takes to build blocks in the network. This metric is useful for monitoring the efficiency of the block building process.  
> [!CAUTION]
> If the metrics does NOT increase, your node have might NOT join consensus.
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/2065141f-a7ea-4d57-a8c2-12cfbf458645" />


### - Grandpa Precommits Total
`substrate_finality_grandpa_precommits_total`  
The metric provides information on the total number of precommits made locally by the GRANDPA (GHOST-based Recursive ANcestor Deriving Prefix Agreement) completion mechanism in the chain.  


### - Grandpa Prevotes Total
`substrate_finality_grandpa_prevotes_total`  
Information on the total number of prevotes made locally by the GRANDPA (GHOST-based Recursive ANcestor Deriving Prefix Agreement) completion mechanism in the chain.  
This metric is useful to monitor the activity and performance of the GRANDPA consensus process in the avail_turing_network chain, providing an indication of the total number of prevotes that have been issued by the validators.  

> [!CAUTION]
> If the metrics does NOT increase, your node dose might NOT join consensus.
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/6809d654-d384-4047-a09b-d6a952a75b9b" />

> [!TIP]
> **_Grandpa Precommits Total and Grandpa Prevotes Total_**  
> You can use the metrics to find if your session key change correctly when you update node using [Safe & Slow upgrade](https://docs.availproject.org/docs/operate-a-node/become-a-validator/upgrade-your-validator#slow--safe-upgrade).  
> I believe the session key update will conduct the next epoch (+1 epoch from the session key updating epoch),  
> and then the Grandpa Precommits Total and Grandpa Prevotes Total will begin to increase another next epoch (+2 epoch from the session key updating epoch).


> [!TIP]
> **_Metrics Detail_**  
> Please refer Cumulo-Pro avail metrics guide for the detail. [Cumulo-Pro Avail Tool](https://github.com/Cumulo-pro/Avail_tools/blob/main/avail-metrics/metrics.md)  

---

# 3. (Optional) Set up notification system: Grafana + PagerDuty
> [!TIP]
> **_PagerDuty_**  
> PagerDuty can automatically group related alerts into a single incident to minimize noise while centralizing relevant context.
> Incident notifications are automatically sent using any preferred combination of phone calls, SMS, push notifications and emails.

> [!CAUTION]
> `Free plan` does not support international `phone` / `SMS` notifications.  
> If you want to receive notification over call or SMS, you have to subscribe `Professional plan` ($ 21 per user/month) [^8]  
> This guide does not support how to sign up PagerDuty.  
> If you does not have the account, please sign up in advance.  
>   
> You do not need to sign up PagerDuty to receive alert at `Slack` / `Discord` / `Telegram`.  
> Please skip "Generate PagerDuty API Key" selction.
[^8]: [PagerDuty Plan and pricing](https://www.pagerduty.com/pricing/incident-response/)


## Generate PagerDuty API Key
### Go to Service Directory
<img width="1680" alt="pagerduty-01" src="https://github.com/qyeah98/voi/assets/99518712/46eef1be-a23f-4173-97b8-6139d8160df7">

### Click +New Service
<img width="1680" alt="pagerduty-02" src="https://github.com/qyeah98/voi/assets/99518712/61de7bd2-3818-4efd-a1a4-b059b8486ac9">

### Input Service Name
<img width="1680" alt="pagerduty-03" src="https://github.com/qyeah98/voi/assets/99518712/614bef36-dc58-4ddb-b22c-3cf8cf376bac">

### Select Escalation Policy
> [!TIP]
> If you want to assign existing escalation rule, please click "Select an existing Escalation Policy".
<img width="1680" alt="pagerduty-04" src="https://github.com/qyeah98/voi/assets/99518712/7cc19224-5dc4-4389-b8b7-ced62aedcc73">

### Reduce Noise
> [!TIP]
> Not every alert should be an incident.  
> PagerDuty’s noise reduction capabilities leverage data science and machine learning to cut down on system noise and reduce alert fatigue.  
> This means fewer incidents and interruptions for on-call responders, richer context around the incidents that do trigger, and lower resolution times.
<img width="1680" alt="pagerduty-05" src="https://github.com/qyeah98/voi/assets/99518712/88f08a20-f8d8-4c13-a98b-97daf2ea69e6">
<img width="1680" alt="pagerduty-06" src="https://github.com/qyeah98/voi/assets/99518712/54ce32fe-c90e-4e3f-9eac-be4c6d1ad710">

### Select integration service
<img width="1680" alt="pagerduty-07" src="https://github.com/qyeah98/voi/assets/99518712/1e77f89c-621e-4752-89da-f0001c775883">
<img width="1680" alt="pagerduty-08" src="https://github.com/qyeah98/voi/assets/99518712/377d20d4-4735-4cc6-9ce4-b46323902062">

### Copy Integration Key
<img width="1680" alt="pagerduty-09" src="https://github.com/qyeah98/voi/assets/99518712/03ef61a0-73c6-40d9-a7c7-c60b1848c2bf">

## Integration Grafana with PagerDuty
### Access your Grafana
Launch browser and go to `http://<YOUR-SERVER-GLOBAL-IP>:3000/`

### Go to Alerting > Contact point
<img width="1680" alt="pagerduty-10" src="https://github.com/qyeah98/voi/assets/99518712/28e245e2-48b3-499f-802f-2fb0da8481f6">
<img width="1680" alt="pagerduty-11" src="https://github.com/qyeah98/voi/assets/99518712/03dff62c-eefa-4250-aa97-13f4fc94bcd2">


### Add Contact Point
<img width="1680" alt="pagerduty-12" src="https://github.com/qyeah98/voi/assets/99518712/9cf9ef03-c65e-4ddf-b382-4c25619bba2d">

> [!TIP]
> When click "Test", you'll receive notification from PagerDuty.  
> If you'd like to change on-call way, please change notification rule at PagerDuty. [^9]  
>  
> If you want to receive alert at `Slack` / `Discord` / `Telegram`, please select `Slack` / `Discord` / `Telegram`
[^9]: [PagerDuty User Profile](https://support.pagerduty.com/docs/user-profile#:~:text=Edit%20a%20Notification%20Rule,-Edit%20any%20notification&text=In%20the%20web%20app%2C%20navigate,you'd%20like%20to%20change.)

<img width="1680" alt="pagerduty-13" src="https://github.com/qyeah98/voi/assets/99518712/6313344c-2b07-4ba2-9c10-7df1c447e6dc">


### Change Grafana Notification policies
<img width="1680" alt="pagerduty-14" src="https://github.com/qyeah98/voi/assets/99518712/2ddb6765-f943-4b52-94d1-d1965a4d7218">
<img width="1680" alt="pagerduty-15" src="https://github.com/qyeah98/voi/assets/99518712/9ba7c55b-002e-43a2-aff9-faa8e663407b">
<img width="1680" alt="pagerduty-16" src="https://github.com/qyeah98/voi/assets/99518712/821cf4db-85f1-4b20-8aa8-6caa51927cf3">


### Creating Alert Rule
> [!TIP]
> _Q. What is Grafana Alerting Rule?_  
> A. An alert rule consists of one or more queries and expressions, a condition, and the duration over which the condition needs to be met to start firing.  
> While queries and expressions select the data set to evaluate, a condition sets the threshold that an alert must meet or exceed to create an alert.

> [!IMPORTANT]
> What metrics you choose is the most important thing to monitoring your node.  
> This guide is monitoring `Best Sync LAG` metrics. 
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/81f9b0e5-5061-4495-8164-52022de9cf39" />
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/2e2a9481-8a2d-4bc7-b7d2-7a27eba9b74d" />
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/82792952-bc7c-46ba-8f3e-c96cb39d403f" />
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/f8940bee-b8e1-47bb-b087-684260cf4925" />
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/ddad8658-ca22-417b-b360-391fea2ab7de" />
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/aa600ad8-e578-4552-a101-a16ff3eef633" />
<img width="1680" alt="Image" src="https://github.com/user-attachments/assets/83837c53-d4e4-4593-9d8f-cefde4b977b2" />

> [!IMPORTANT]
> To Keep network healhy, it is the important to detect aberrations in node metrics as soon as possible.  
> If you set monitor and notification, it lead to make network robustness.  
> I believe this is the minimum responsibility for node operators.
