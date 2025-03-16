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

 ## Attack Types
 In order to build our Bayesian Network structure, first we need to understand how different attack types work. <br/>
 - __*Probe attacks*__ are aimed at gathering information about the target network from a source that is often external to the network.
 - __*Denial-of-Service (DoS)*__ attacks results in an interruption of the service by flooding the target system with illegitimate requests.
 - __*Remote-to-Local (R2L)*__ is the attempt to gain illegal access to a system’s account by exploiting its vulnerabilities.
 - __*User-to-Root (U2R)*__ occurs when a user tries to gain super user privilege .

## Bayesian Network Structure<br/>
### Why did we choose these features to affect intrusion and not others?
The features were chosen because they provide the strongest separation between normal and attack traffic:

They represent core network anomalies that cover different attack types:

 - DoS attacks → src_bytes, dst_bytes, serror_rate
 - Probe attacks → same_srv_rate, rerror_rate
 - R2L attacks → num_failed_logins
 - U2R attacks → num_compromised

They are interpretable and directly measurable. These features come from packet-level or session-level statistics, making them easily observable in a real-time system.<br/>
They also avoid redundancy while maximizing attack coverage. Features like dst_host_srv_count might be useful, but they correlate highly with same_srv_rate, so only one is needed.<br/>

__*src_bytes:*__ number of data bytes from source to destination.<br/>
 - Low src_bytes are common in probe attacks, where a system aims to gather information from the target by seding small packets.<br/>
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
__*num_compromised:*__<br/>
 - A high num_compromised suggests a successful exploit that gained unauthorized access to system files.<br/>
__*num_shells:*__ number of shell prompts started.<br/>
 - U2R attacks often involve spawning a shell to execute root commands.<br/>
__*root_shell:*__ whether a root shell was obtained.<br/>
 - Root access means complete takeover. If root_shell = 1, then U2R might've been successful already.<br/>
 
 
# Sources
 - https://www.sciencedirect.com/science/article/pii/S1877050918301637/pdf?md5=01cd358af58d0786a80be6fb0b09e310&pid=1-s2.0-S1877050918301637-main.pdf
 - https://kdd.ics.uci.edu/databases/kddcup99/task.html


