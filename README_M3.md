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

 ## Attack Types
 In order to build our Bayesian Network structure, first we need to understand how different attack types work. <br/>
 - __*Probe attacks*__ are aimed at gathering information about the target network from a source that is often external to the network.
 - __*Denial-of-Service (DoS)*__ attacks results in an interruption of the service by flooding the target system with illegitimate requests.
 - __*Remote-to-Local (R2L)*__ is the attempt to gain illegal access to a system’s account by exploiting its vulnerabilities.
 - __*User-to-Root (U2R)*__ occurs when a user tries to gain super user privilege .

## Bayesian Network Structure<br/>
### src_bytes: number of data bytes from source to destination.<br/>
 - Low src_bytes are common in probe attacks, where a system aims to gather information from the target by seding small packets.
 - High src_bytes are commin in DoS attacks, where a system floods the target with massive amount of data.
### dst_bytes: number of data bytes from destination to source<br/>
 - Low dst_bytes are common in DoS attacks, since the victim often doesn't respond (SYN flood).
### same_srv_rate: % of connections to the same service. <br/>
 - Very low same_srv_rate may indicate scanning behavior from a probe attack.
### serror_rate: % of connections that have SYN errors. <br/>
 - High serror_rate might indicate a SYN flood attack, which is a type of DoS.
### rerror_rate: % of connections that have REJ errors (connection attempts refused by the destination). <br/>
 - High rerror_rate may indicate port scanning from a probe attack.
### num_failed_logins: number of failed login attempts. <br/>
 - Very high num_failed_logins may indicate an attempt of a brute force attack, a type of R2L attack.
### num_compromised: number of compromised conditions. <br/>
 - High num_compromised is the strongest indicator of an onging U2R attack. <br/>
 



