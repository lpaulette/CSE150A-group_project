# Probabilistic Agent code
https://colab.research.google.com/drive/1kug9L71UTLJ-ZIk268No6ggnFMnl46f2?usp=sharing

# Abstract
This probabilistic agent will detect anomalies in network traffic using a Bayesian Network Model trained with Maximum Likelihood Estimation (MLE). The agent will assign probability scores to network flows and classify them as "normal" or "attack" based on learned probabilistic dependencies.

We will use the KDD Cup 1999 dataset: http://kdd.ics.uci.edu/databases/kddcup99/kddcup.data_10_percent.gz

The Bayesian Network will analyze the following network traffic features to determine intrusion likelihood:

 - Packet size (Source and destination bytes)
 - Connection duration
 - Service request patterns (Same service rate, SYN error rate, REJ error rate)
 - User authentication failures (Failed logins)
 - Host compromise indicators (Compromised machine count) <br/>
 
The agent will learn conditional probability distributions (CPDs) of these network features and compute: <br/>

*𝑃 (intrusion ∣ network sample)* <br/>

If the probability exceeds a threshold, the agent will classify the sample as an intrusion and identify its most likely attack type (DoS, Probe, R2L, U2R). It will do this by choosing the highest conditiona probability: <br/>

*P (attack type | intrusion)* <br/>

## PEAS: <br/>
 - __*Performance:*__ The agent is evaluated based on Accuracy, False Positive Rate (FPR), False Negative Rate (FNR). High accuracy and low FPR/FNR indicate an effective model. <br/>
 - __*Environment:*__ The agent operates in a network security environment, analyzing real-time or historical network traffic logs. The dataset includes both normal and attack traffic patterns from KDD99. <br/>
 - __*Actuators:*__ The agent raises an alert when an intrusion is detected. It also classifies the attack type to help security teams respond appropriately. <br/>
 - __*Sensors:*__ The agent observes network traffic features from the dataset, including packet size, connection duration, service request patterns, TCP errors, login failures, and host compromise indicators.<br/>

The dataset has 42 features, where 41 of them are network features, from protocol type (i.e. TCP), to dst_host_srv_rerror_rate. The 42nd feature is "label", which indicates whether the network sample is normal or an attack, with exactly which attack type.

There are 23 unique attack types that fall into four main categories: DoS, R2L, U2R, and probing.

 ## Attack Types
 In order to build our Bayesian Network structure, first we need to understand how different attack types work. <br/>
 - __*Probe attacks*__ are aimed at gathering information about the target network from a source that is often external to the network.
 - __*Denial-of-Service (DoS)*__ attacks results in an interruption of the service by flooding the target system with illegitimate requests.
 - __*Remote-to-Local (R2L)*__ is the attempt to gain illegal access to a system’s account by exploiting its vulnerabilities.
 - __*User-to-Root (U2R)*__ occurs when a user tries to gain super user privilege .

## Bayesian Network Setup<br/>
![EM algorithm](https://github.com/user-attachments/assets/9584d8b5-9c06-4a13-80af-2f130ada95dd)<br/>

### Why did we choose these features to affect intrusion and not others?
The features were chosen because they provide the strongest separation between normal and attack traffic:

They represent core network anomalies that cover different attack types:

 - DoS attacks → src_bytes, dst_bytes, serror_rate
 - Probe attacks → same_srv_rate, rerror_rate
 - R2L attacks → num_failed_logins
 - U2R attacks → num_compromised

They are interpretable and directly measurable. These features come from packet-level or session-level statistics, making them easily observable in a real-time system.<br/>
They also avoid redundancy while maximizing attack coverage. Features like dst_host_srv_count might be useful, but they correlate highly with same_srv_rate, so only one is needed.<br/>

__*src_bytes:*__ number of data bytes from source to destination.
 - Low src_bytes are common in probe attacks, where a system aims to gather information from the target by seding small packets.
 - High src_bytes are commin in DoS attacks, where a system floods the target with massive amount of data.<br/>
 
__*dst_bytes:*__ number of data bytes from destination to source<br/>
 - Low dst_bytes are common in DoS attacks, since the victim often doesn't respond (SYN flood).<br/>
 
__*same_srv_rate:*__ % of connections to the same service. <br/>
 - Very low same_srv_rate may indicate scanning behavior from a probe attack.<br/>
 
__*serror_rate:*__ % of connections that have SYN errors. <br/>
 - High serror_rate might indicate a SYN flood attack, which is a type of DoS.<br/>
 
__*rerror_rate:*__ % of connections that have REJ errors (connection attempts refused by the destination). <br/>
 - High rerror_rate may indicate port scanning from a probe attack.<br/>
 
__*num_failed_logins:*__ number of failed login attempts. <br/>
 - Very high num_failed_logins may indicate an attempt of a brute force attack, a type of R2L attack.<br/>
 
__*num_compromised:*__ number of compromised conditions. <br/>
 - High num_compromised is the strongest indicator of an onging U2R attack. <br/>

### Why did we choose these features to affect U2R and not others?
The three features we chose are directly related to privilege escalation, which is how U2R attacks.<br/>

__*num_compromised:*__
 - A high num_compromised suggests a successful exploit that gained unauthorized access to system files.<br/>
 
__*num_shells:*__ number of shell prompts started.
 - U2R attacks often involve spawning a shell to execute root commands.<br/>
 
__*root_shell:*__ whether a root shell was obtained.
 - Root access means complete takeover. If root_shell = 1, then U2R might've been successful already.<br/>

 ### Why did we choose these features to affect DoS and not others?
 Denial-of-Service attacks aim to overwhelm a system by sending excessive traffic, preventing legitimate users from accessing services. The selected features correlate to DoS attack patterns because they capture high traffic volume and repeated failed connection attempts.<br/>

 __*serror_rate:*__ 
  - Dos attacks often send large numbers of SYN requests without completing connections, leading to a high serror_rate.<br/>

 __*count:*__ Number of connections from the same source within a short time.
  - DoS attacks typically generate rapid, repeated connection attempts, resulting in a high count value compared to normal traffic. <br/>

 __*srv_count:*__ 	Number of connections to the same service.
  - Many DoS attacks target a specific service, repeatedly sending requests to crash it. High srv_count suggests a single-service DoS attack.<br/>

### Why did we choose these features to affect probe and not others?
Probe attacks work by gathering information by sending different types of requests and analyzing responses.<br/>

__*same_srv_rate:*__
 - Low same_srv_rate suggests scanning behavior. The attacker tries many services, unlike normal traffic, which often connects to the same service.<br/>

__*diff_srv_rate:*__ the percentage of connections using different services.
 - High diff_srv_rate is a strong indicator of probing multiple services The attackers look for open ports across different protocols.<br/>

__*srv_diff_host_rate:*__ the percentage of connections to different hosts for the same service.
 - High srv_diff_host_rate suggests the attacker is scanning multiple machines looking for vulnerable hosts.<br/>

__*rerror_rate:*__
 - High rerror_rate suggests many failed access attempts, which is common in Probe attacks trying to access closed or protected services.<br/>

### Why did we choose these features to affect R2L and not others?
A Remote-to-Local (R2L) attack happens when an external attacker gains unauthorized access to a local machine. These attacks often involve brute-force login attempts or exploiting misconfigured services to steal sensitive data.<br/>

__*num_failed_logins:*__ 
 - Brute-force attacks cause high num_failed_logins, since the attacker repeatedly tries different passwords.<br/>

__*logged_in:*__ this feature indicates whether the session is authenticated.
 - A successful R2L attack will eventually set logged_in = 1 after many failed attempts.<br/>


__*num_access_files:*__ this feature indicates the number of sensitive files accessed in he session.
 - If an attacker successfully logs in, they will attempt to steal data, leading to a high num_access_files count.<br/>

# Data preprocessing

First we categorized the attack types into one of the four main categories as follows:<br/>

<img width="575" alt="image" src="https://github.com/user-attachments/assets/edb0e190-5152-4b4e-b71e-ed4a58b10783" /><br/>
<img width="266" alt="image" src="https://github.com/user-attachments/assets/f6de3688-a43c-40d7-a5a7-f6762812db5b" />

Then we used __*pd.get_dummies*__ to divide the "attack_category" feature into binary columns as follows:<br/>
<img width="466" alt="image" src="https://github.com/user-attachments/assets/d71784d3-e92d-499e-900f-ced171a0c01f" /><br/>
<img width="221" alt="image" src="https://github.com/user-attachments/assets/5b7105a3-713c-44bc-8ed1-c7ab08d09afb" />

After that we create the feature "intrusion", which is True if there is an attack going on and False else.<br/>
<img width="457" alt="image" src="https://github.com/user-attachments/assets/f9ad67c9-1502-4d1f-b81b-abdc3d22db97" /><br/>

We reduced the size of the dataframe, to only contain the features that indicate any kind of network intrusion.
<img width="524" alt="image" src="https://github.com/user-attachments/assets/f058e92c-3335-4866-a214-91ebafccb1f9" /><br/>

We categorized the rest of the continuous features into categorical ones, as "very low" to "very high", depending on the range of each feature. We used GMM to categorize the values into probability-based bins.
<img width="389" alt="image" src="https://github.com/user-attachments/assets/febee4cc-d74b-4c4b-923e-4dc32e72a1bc" />
<br/>

We avoided the features with too few unique variables, such as "num_shells", "logged_in" and "root_shell".<br/>

Then we categorize the binary features:<br/>
<img width="536" alt="image" src="https://github.com/user-attachments/assets/5dcf35b4-03d4-496f-955f-5e2e4263fd0b" /><br/>

And finally "num_shells", which has 3 unique values:<br/>
<img width="555" alt="image" src="https://github.com/user-attachments/assets/15d1acfc-7e06-4dc4-b566-43d98c2bbcd3" />


# Training setup

First we defined the structure of the Bayesian Model.<br/>
<img width="302" alt="image" src="https://github.com/user-attachments/assets/34e43e47-a14a-4c81-b500-8a8bb4f43ce9" /><br/>

We used Maximum Likelihood Estimator to train the model.<br/>
<img width="284" alt="image" src="https://github.com/user-attachments/assets/03465729-cde6-4e90-b819-942bb5edac3b" />

The MaximumLikelihoodEstimator is used to train the BayesianNetwork. It estimates the conditional probability distributions (CPDs) by counting occurrences of feature values in the dataset.
This approach allows the Bayesian Network to learn relationships between features and intrusion probability based on real network traffic patterns.

# Results

# What's next

# Sources
 - https://www.sciencedirect.com/science/article/pii/S1877050918301637/pdf?md5=01cd358af58d0786a80be6fb0b09e310&pid=1-s2.0-S1877050918301637-main.pdf
 - https://kdd.ics.uci.edu/databases/kddcup99/task.html


