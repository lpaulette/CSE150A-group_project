# Abstract
This probabilistic agent will detect anomalies in network traffic using a Bayesian Network Model trained with Maximum Likelihood Estimation (MLE). The agent will assign probability scores to network flows and classify them as "normal" or "attack" based on learned probabilistic dependencies.

We will use the KDD Cup 1999 dataset: http://kdd.ics.uci.edu/databases/kddcup99/kddcup.data_10_percent.gz

The Bayesian Network will analyze the following network traffic features to determine intrusion likelihood:

Packet size (Source and destination bytes)
Connection duration
Service request patterns (Same service rate, SYN error rate, REJ error rate)
User authentication failures (Failed logins)
Host compromise indicators (Compromised machine count)
The agent will learn conditional probability distributions (CPDs) of these network features and compute: <br/>
*𝑃(intrusion ∣ network sample)*
If the probability exceeds a threshold, the agent will classify the sample as an intrusion and identify its most likely attack type (DoS, Probe, R2L, U2R).

