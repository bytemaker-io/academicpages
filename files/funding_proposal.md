# Funding Proposal

## Basic Information

### **First Name:** Fei
### **Last Name:** Wang
### **Email:** bytemaker.io@gmail.com
### **Are you submitting on behalf of a team, or as an individual?**  
    I don't know
### **Individual or team summary:** 

    I, Wang Fei, a MSc student at Budapest University of Technology, SH (Stipendium Hungaricum) scholarship holder. I have several years of experience in system modeling and software development, enabling me to translate theoretical models into practical, secure, and efficient tools. I have conducted academic research in P2P networks and applied cryptography under the supervision of Professor János Tapolcai and Dr. Bence Ladóczki.

### **City:** Budapest
### **Country:** Hungary    
### **Website:** Https://wangfei.dev


## Project Information

### **Project category:** Consensus layer
### **Project name:** Decentralized Collection of Attestations for Single-Slot Finality in Proof-of-Stake Blockchains
### **Project repo:** Not open-sourced yet because the paper has not been published.
### **Previous work:** 

    The Ethereum Research article published by János Tapolcai discusses a flooding protocol aimed at efficiently collecting attestations from validators within a single slot (12 seconds) without requiring high-bandwidth connections [1]. The protocol focuses on achieving rapid finality with minimal bandwidth, hiding validator IP addresses, providing succinct proof of supermajority, and being resilient against malicious nodes. It uses an alternative signature aggregation scheme that leverages efficient data structures and Rice-Golomb coding for storage optimization. Simulations demonstrate the protocol’s effectiveness in achieving finality and bandwidth efficiency.

    We have implemented a discrete event simulator in low level C++ and Rust that uses Ethereum’s network topology to emulate block proposal, signature collection, and aggregation events. Using the Nebula crawler [2], we mapped the libp2p topology with 9,294 nodes and 934,266 links. Since the exact number of validators per consensus client is obscured, we infer it from the count of attestation pub-sub channels a node subscribes to. Nodes with 64 subscriptions are presumed to have a high number of validators, capped at 256. We assigned V = 1,000,000 validators to nodes based on their attestation pub-sub channel subscriptions.

    The approach we adopted differs from the original proposal in that we replaced Huffman coding with Rice-Golomb coding. Using Huffman coding would require maintaining a Huffman tree, and encoding 2^22 validator IDs with it would result in an extremely large tree. In our simulation involving a million validator IDs, we observed an average storage requirement of ∼ 11 bits per ID. This method exhibits approximately 45% space savings when compared to the straightforward approach of storing validator IDs using a 20-bit binary representation.

    In our simulations, we have demonstrated that the data structure used meets the specific time and space requirements of our protocol optimally. And, we conducted a comprehensive simulation with 9,294 nodes and 1,000,000 validators, proving the feasibility of our proposed method for achieving single-slot finality within 12 seconds.

    References

    [1] János Tapolcai. Flooding protocol for collecting attestations in a single slot. https://ethresear.ch/t/flooding-protocol-for-collecting-attestations-in-a-single-slot/17553/1, November 2023.

    [2] Dennis Trautwein, Aravindh Raman, Gareth Tyson, Ignacio Castro, Will Scott, Moritz Schubotz, Bela Gipp, and Yiannis Psaras. Design and evaluation of ipfs: a storage layer for the decentralized web. In Proceedings of the ACM SIGCOMM 2022 Conference, SIGCOMM’22. ACM, August 2022.

    Since our paper has not been officially published yet, the code has not been made open source.


### **What is the project?**

    This project builds upon our previous work, with the primary objective of verifying the effectiveness of our proposed method within a formal Ethereum network. Following the successful completion of the Merge on September 15, 2022, libp2p was officially integrated into the Ethereum mainnet’s networking layer [1]. In our initial simulator, we overlooked certain features of libp2psub, such as Peer Types, Grafting, and Pruning [2], and assumed that peer connections would remain stable without disconnections during data collection.

    In this proposal, we aim to address these limitations by conducting experiments in a more realistic network environment. We will incorporate the dynamic aspects of libp2psub, including various peer types and the mechanisms of Grafting and Pruning. Additionally, we will simulate peer disconnections and reconnections to assess the robustness and reliability of our method under different network conditions. Our goal is to validate the scalability and efficiency of our approach in maintaining optimal network performance and ensuring effective message propagation in the Ethereum mainnet.

    We plan to deploy approximately 10,000 real libp2p nodes with around 1 million validators. This deployment will allow us to re-evaluate our proposed method in a realistic network environment. We will also conduct experiments to verify the effectiveness of our method in achieving single-slot finality within 12 seconds. Our analysis will focus on bandwidth efficiency, storage optimization, and message propagation. Ultimately, we aim to demonstrate the feasibility of our method in achieving rapid finality with minimal bandwidth and storage requirements in a real-world Ethereum network.

    References

    [1] Prithvi Shahi. libp2p and ethereum: The merge. https://blog.libp2p.io/libp2p-and-ethereum #how-ethereum-beacon-nodes-use-libp2p, January 2023. Accessed: 2024-06-11.

    [2] libp2p. What is publish/subscribe. https://docs.libp2p.io/concepts/pubsub/overview/, 2024. Accessed: 2024-06-11.

### **What problem(s) are being solved by within the scope of the grant?**

    The messaging protocol used by the Beacon Chain in Ethereum is GossipSub v1.1 [1, 2]. Our primary objective is to verify whether the proposed protocol works effectively under the GossipSub v1.1 protocol and whether it performs better in a real network environment. In our previous simulator implementation, we only implemented the simplest flood routing protocol, assuming all peer nodes were full-message peers. We did not implement mechanisms such as gossiping, grafting, and pruning, which might have resulted in slightly worse simulation outcomes. Moreover, GossipSub v1.1 implements Flood Publishing, Adaptive Gossip Dissemination, and Peer Scoring, which could potentially enhance the performance of our proposed method.

    Additionally, we aim to evaluate how our protocol performs in a dynamic network environment. In the previous simulator, we located nodes based on their IP addresses and calculated the geographical distances between them. We assumed that the cable length connecting two nodes was twice their physical distance and that data transmission occurred at the speed of light. Furthermore, we considered a constant additional delay of 10 milliseconds for each link to account for other latencies. However, we did not consider fluctuations in network latency and always assumed that network links remained stable.

    There are several issues we aim to address:
    1. Verify whether it is possible to achieve finality within 12 seconds in GossipSub v1.1 protocol when the degree of full-message peer connections ranges from 4 to 12.

    2. nvestigate the optimal message size in Pub/Sub protocols, as excessively large messages may lead to nodes receiving duplicate messages, thereby increasing network load [3].

    3. Peers gossip about messages they have recently seen. Every second, each peer randomly selects 6 metadata-only peers and sends them a list of recently seen messages. Investigate the broadcast frequency.

    4. Investigate how our method performs under Adaptive Gossip Dissemination.

    5. Evaluate the performance of our proposed method under limited bandwidth and high-load network environments.

    We have implemented our proposed method using the Rust libp2p library. However, we are currently facing the challenge of insufficient computational resources to launch enough libp2p instances. We estimate that we need approximately 500 servers, each with 16 cores and 32GB of RAM, to build a network with around 10,000 nodes with over 1 million validators in the network.

    Based on the results from our previous simulator runs, 70% of the computational time is primarily consumed by Rice-Golomb encoding and decoding. We expect that each libp2p instance should have at least one CPU core available.

    Manually deploying over 10,000 libp2p instances is impractical. Therefore, we plan to use Kubernetes [4] to deploy 500 computing nodes, each hosting approximately 20 libp2p instances. Using the tc tool [5], we can easily set limited bandwidth, network latency, and packet loss rate for each node, making our experiments more representative of real network environments.

    References

    [1] libp2p. Gossipsub v1.1. https://github.com/libp2p/specs/blob/master/pubsub/gossipsub/gossipsub-v1.1.md, 2020 Accessed: 2024-06-11.

    [2] Prithvi Shahi. libp2p and ethereum: The merge. https://blog.libp2p.io/libp2p-and-ethereum #how-ethereum-beacon-nodes-use-libp2p, January 2023. Accessed: 2024-06-11.

    [3] Whyrusleeping. Issue comment on github. https://github.com/libp2p/specs/issues/118#issuecomment-499688869, 2019. Accessed: 2024-06-11.

    [4] Kubernetes Documentation. Kubernetes official documentation. https://kubernetes.io/. Accessed: 2024-06-11.

    [5] Linux Documentation Project. Traffic control howto. https://tldp.org/HOWTO/Traffic-Control-HOWTO/intro. Accessed: 2024-06-11.

### **Why is your project important?**

    After successfully transitioning from proof-of-work to proof-of-stake, the Ethereum blockchain developer community set an ambitious goal: achieving rapid block finalization.

    The current lengthy finalization time has proven unsuitable for most users. Typically, users are unwilling to wait 15 minutes for transaction confirmations, which is particularly inconvenient for applications and exchanges requiring high transaction throughput. Furthermore, the delay between block proposal and finalization increases the likelihood of transient reorganization, which attackers can exploit to censor specific blocks or extract maximum extractable value (MEV). The mechanism for handling phased block upgrades is also quite complex, and multiple patches to address security vulnerabilities have made this one of the more error-prone parts of the Ethereum codebase. By reducing the finalization time to a single slot, these issues can be effectively mitigated.

### **How does your project differ from similar ones?**

    Currently, the following two proposals are being discussed within the Ethereum community:

    1. Super-committees
    2. Validator set size capping

    Super-committees drawbacks: Super-committees involve only a subset of validators participating in each round reducing the total number of messages required. However, this approach has complexity costs, including the need for additional protocol code and potential incentives for validators to stall finality during high-fee periods.

    Validator Set Size Capping drawbacks: Validator set size capping limits the total number of validators. This can create issues such as discouragement attacks and potential unfairness to small stakers. Economic capping can also distort validator incentives and lead to a higher risk of marginal validators dominating the network.

    Our research presents an alternative scheme that has the potential to achieve the desired single-slot finality Ours is a fully distributed approach in which no node has a specific role, rendering it more robust than the current one. 


### **Requested amount:** €20,000

### **Proposed tasks, roadmap and budget**

    Because we have implemented our proposed method using the Rust libp2p library, there is no need to develop new software. We will focus on deploying a large-scale network to verify the effectiveness of our method in achieving single-slot finality within 12 seconds. The proposed tasks are as follows:

    Tasks:

    1. Buying 500 cloud servers, each with 16 cores and 32GB of RAM, to build a network with around 10,000 nodes while ensuring there are over 1 million validators in the network. 

    2. Deploying 500 k8s nodes, each hosting approximately 20 libp2p instances.

    3. Using the tc tool to set limited bandwidth, network latency, and packet loss rate for each node.

    4. Conducting experiments to verify the effectiveness of our method in achieving single-slot finality within 12 seconds.

    5. Analyzing the results and merging them with our previous work. 

    Roadmap:

    1. July 1-3, 2024: Purchase 500 cloud servers, each with 16 cores and 32GB of RAM.
    2. July 4-6, 2024: Deploy 500 k8s nodes, each hosting approximately 20 libp2p instances. Use the tc tool to set limited bandwidth, network latency, and packet loss rate for each node.
    3. July 7-31, 2024: Conduct experiments to verify the effectiveness of our method in achieving single-slot finality within 12 seconds.
    4. August 1-15, 2024: Analyze the results and merge them with our previous work.
    5. August 16-31, 2024: Report writing and submission.

    Budget:

    Based on the pricing of Hetzner, the cheapest cloud provider in Europe, the CX52 server has 16 CPU cores and 32GB of memory. Its price is €32.4 per month.

    500 servers * €32.4 = €16,200

    We estimate that completing this task will take at least one month, as we will continuously adjust our algorithm to adapt to the most realistic Ethereum network.

    The reason why we are applying for a grant of 20,000 euros to meet additional needs. For example, we may need to purchase additional servers or extend the duration of the experiment. We will provide a detailed report on how the funds were used. 

    Deliverables:

    1. A detailed report on the effectiveness of our proposed method in achieving single-slot finality within 12 seconds.

    2. A merged report combining the results of our previous work with the new experiments.

    3. A blog post summarizing the key findings of our research.

    4. A presentation at the Ethereum Research Workshop.

    5. A paper submission to the conference.

    6. All reviwed code and data used in the experiments will be made open source after the paper is published.

### **Is your project a public good?**
    
    Yes, our project is a public good. Our research aims to improve the efficiency and scalability of the Ethereum network, benefiting all users and developers. By reducing the finalization time to a single slot, we can enhance the user experience and security of the network. Our proposed method is fully distributed, making it more robust and secure than existing solutions. We believe that our research will have a positive impact on the Ethereum community and contribute to the development of decentralized applications.

### **Is your project open source?**
    
    Our project is not open source yet because the paper has not been published. However, we plan to make all reviewed code and data used in the experiments open source after the paper is published. We believe that open sourcing our work will benefit the Ethereum community by enabling developers to build on our research and contribute to the development of decentralized applications.

### **What are your plans after the grant is completed? How do you aim to be sustainable after the grant? Alternatively, tell us why this project doesn't need to be sustainable!**

    After the grant is completed, we plan to continue refining our research and applying it to real-world blockchain networks. We intend to submit our research to the Ethereum Foundation and present it at the Ethereum Research Workshop. We also plan to publish our research on our blog to share it with the community. We believe that our research will have a positive impact on the Ethereum community and contribute to the development of decentralized applications. We will continue to refine our algorithm, and if our research is well received, we can help Ethereum developers integrate our research into their projects.
    
### **If you didn't work on this project, what would you work on instead?**
    
    If I didn't work on this project, I would focus on my academic research in P2P networks and applied cryptography. I am particularly interested in exploring the security and privacy implications of decentralized systems and developing practical solutions to address these challenges. I would also continue to contribute to open source projects and collaborate with other researchers to advance the field of distributed systems. I am passionate about building secure and efficient systems that empower users to take control of their data and protect their privacy. I believe that my background in system modeling and software development equips me with the skills and knowledge to make meaningful contributions to the field of decentralized systems.    

### **Have you previously applied to ESP with this same idea or project?**

    I don't know


### **If you've applied previously with the same idea, how much progress have you made since the last time you applied?**

    I don't know

### **Have you applied for or received other funding?**
    
    I don't know    


    








