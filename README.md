# Data-Driven Model Predictive Control for Autonomous Vehicle Steering

**[Jiarui Zhang](https://tops.tongji.edu.cn/info/1132/1815.htm)**

[Department of Traffic Engineering and Key Laboratory of Road and Traffic Engineering, Ministry of Education, Tongji University](https://tops.tongji.edu.cn/)  

## Abstract

With the development of autonomous driving technology, there are increasing demands for vehicle control, and MPC has become a widely researched topic in both industry and academia. Existing MPC control methods based on vehicle kinematics or dynamics have challenges such as difficult modeling, numerous parameters, strong nonlinearity, and high computational cost. To address these issues, this paper proposes a Data-Driven MPC control method for autonomous vehicle steering. This method avoids the need for complex vehicle system modeling and achieves trajectory tracking with relatively low computational time and small errors. We validate the control effectiveness of our algorithm in specific scenario through CarSim-Simulink simulation and perform comparative analysis with PID and vehicle kinematics MPC, confirming the feasibility and superiority of the proposed algorithm.

## Problem Statement

The development of autonomous vehicle technology relies on efficient and reliable control algorithms. Early control algorithms primarily depended on PID controllers, which often fell short in complex and dynamic driving environments. MPC stands out due to its ability to calculate high-precision control inputs within a limited prediction horizon based on the vehicle model and reference trajectory. However, traditional vehicle models, especially dynamic ones, are challenging to model and calibrate, often leading to significant computational costs. To address this, our proposed Data-Driven MPC for autonomous vehicle steering control leverages the Hankel matrix constructed from offline input-output trajectory data, bypassing the need for complex system models and enhancing prediction accuracy and computational efficiency.

![flowchart](./src/flowchart.png)

## Methodology
In the article, we give an introduction to Willems' lemma and then point out that Berberich et al. designed a data-driven MPC method based on it. Then, we modify this data-driven MPC so that it can be suitable for steering control of self-driving vehicles, and give the framework of the designed algorithm with a detailed description of the algorithm.

## Experiments
Since the algorithm is mainly based on data, the source of data directly determines the effectiveness of the algorithm. The open-loop input-output data acquisition of the vehicle in the simulation environment is accomplished by building a scene in CarSim and selecting a D-class vehicle and a driver model that can realize unbiased turning.The CarSim scene, vehicle and driver model are shown in the following figure.

![scenario](./src/scenario.png)

[](<img src="https://github.com/John0915aaa/Data-Driven-Model-Predictive-Control-for-Autonomous-Vehicle-Steering/blob/pages/src/scenario.png" width="300px">)

In the closed-loop algorithm simulation verification stage, the collected data was preprocessed to remove outliers and interpolate sparse data points. The Data-Driven MPC algorithm was then tested in a dual-lane switching scenario, with the results compared against PID and vehicle kinematics MPC control algorithms. The simulations showed that the DDMPC algorithm provided smooth steering angles, maintaining vehicle stability and comfort while quickly responding to emergency lane changes. The comparative analysis demonstrated that the DDMPC algorithm ensured more accurate trajectory tracking with smaller error variations and significantly reduced computation time compared to the vehicle kinematics MPC algorithm. This highlighted the practical value and superiority of the proposed DDMPC algorithm in real-world driving scenarios.

### Ablation experiment on the Shapley value
In order to facilitate the reader to understand how Shapley value is constantly changing in the process of cooperation, we use the shade of color to represent the value of the Shapley value. More specifically, the closer the color of the vehicle is to purple, the higher its Shapley value is at this moment. This indicates that the vehicle currently has a greater impact on the system. Conversely, lighter colors indicate weaker impacts on the system. Additionally, each car's normalized Shapley value is displayed next to it. The cooperation case shown in Fig.5 in the paper is shown below  

<div align=center>
| <video muted controls width=380> <source src="./src/ablation-case.mp4"  type="video/mp4"> </video> |
</div>


### Comparison under different ROP
In this subsection, we compare the baseline and AWSW-PG(T=1) from an aggregate point of view at first. The results indicate that the implementation of the adaptive weight method provides efficient improvement for collision avoidance. A specific case comparison is shown below  

| <video muted controls width=380> <source src="./src/baseline-case1.mp4"  type="video/mp4"> </video> <video muted controls width=380> <source src="./src/AWSW-PG(T=1)-case1.mp4"  type="video/mp4"> </video> |

As observed in the video, collisions occur when CAVs are driven by the baseline model due to their inability to correct HDV estimates in real time. However, we acknowledge that driving is a sequential decision-making process, and selecting an appropriate planning time can effectively prevent shortsighted behavior. Moreover, in our paper, we mention that though the adaptive weight method successfully helps CAVs avoid collision, it sacrifices efficiency and causes deadlocks.

Thus, we have extended the planning horizon to 8 steps. Additionally, we also present a case to visualize this improvement 

| <video muted controls width=380> <source src="./src/AWSW-PG(T=1)-case2.mp4"  type="video/mp4"> </video> <video muted controls width=380> <source src="./src/AWSW-PG(T=8)-case2.mp4"  type="video/mp4"> </video> |

### Experiments with heterogeneous HDVs involved
Considering that CAVs will continually mix with HDVs that possess various driving abilities, and styles in a long time. we further investigate our model by introducing heterogeneous human-driven vehicles into the background traffic. Specifically, we use orange, blue, and green to represent aggressive, neutral, and conservative drivers respectively (unknown information for connected autonomous vehicles). The transparency of colors indicates the discrepancy between actual actions and predicted actions. When the discrepancy between actual and predicted actions is relatively minor, HDV will be marked with a √ symbol.


| <video muted controls width=380> <source src="./src/h-hdvs-case1.mp4"  type="video/mp4"> </video> <video muted controls width=380> <source src="./src/h-hdvs-case2.mp4"  type="video/mp4"> </video> |


## Citation
```
@misc{,
      title={}, 
      author={},
      year={2023},
      eprint={},
      archivePrefix={arXiv},
      primaryClass={cs.MA}
}
```

## Appendix

### 1.Modeling Heterogeneous HDV
Despite the uncertainty due to the absence of traffic lights, unpredictable and uncontrollable HV latent intention of decision may lead to even greater strait for CAVs. In order to validate the cooperative driving algorithm of CAVs in heterogeneous mixed traffic. In this section, sophisticated real-world drivers' demonstrations were extracted for reproducing heterogeneous HV decisions. 

The main process consists of four steps. Firstly, interaction data from a real-world intersection were collected. Secondly, drivers' decisions were classified into three groups by K-means cluster. Then, non-cooperative game, known as a promising model to reproduce the process of human decision making, was selected to guide the generation of HVs' decision. Finally, the decision-making preference of each group was calibrated through IRL. 

#### Drivers' Decisions Classification by K-means Cluster
In order to reproduce drivers with different decision-making preferences. The first priority was to determine how many decision clusters the drivers at the intersection of XXJH can be divided into. Based on the result of the elbow method, a simple but versatile theory to determine the number of clusters using the Sum of Squared Errors (SSE), three clusters work best for our data. Considering that speed and acceleration are the most intuitive manifestation of decision making during driving, the average, maximum, minimum, and standard deviation of speed and acceleration of vehicles were taken as the clustering parameters}. K-means clustering was then used to classify naturalistic human driving data samples in XXJH.

After the clustering algorithm converged, the drivers' decisions in XXJH were divided into three clusters. Furthermore, their trajectories were used as expert demonstrations and then compared with HDVs' feasible trajectories to calibrate different decision-making preferences. In this paper, a non-cooperative game was chosen to estimate possible decisions human may adopt when confronted with other drivers because it achieves more realistic human behavior when performing conflicting maneuvers at intersections. Together with vehicle dynamics, feasible trajectories of each frame can be concluded.

#### HDVs' Feasible Trajectories Generation through Non-cooperative Game
As mentioned before, game theory describes human as a rational decision-maker who takes action dependencies into account. Hence, there is a growing application in the field of modeling human decision. In this paper, we regard human drivers as rational decision-makers and are aware of the consequence that their actions will influence other drivers who have conflict with them in temporal and spatial dimensions. Non-cooperative game was therefore established to mimic how drivers conjecture and compete with each other while driving in sharing space. We considered a sampling interval, $\Delta t=0.1s$, and an action set including six strategies that represent common driving maneuvers in urban traffic is listed below. 

Table. Action set

![Actionset](./src/Actionset.png)

[](<img src="https://github.com/FanGShiYuu/AWSW-PG/blob/pages/src/Actionset.png" width="320px">)


#### Decision-making Preference Calibration by IRL
After dividing drivers into groups and generating feasible trajectories through non-cooperative game. IRL was introduced to excavate inherent characteristics that influence the expert decision. IRL was proposed later than behavior cloning. Though they share many similarities. Differing from simply imitating expert maneuvers, IRL tries to infer the reason why experts make their decision and then optimize the strategy. In other words, except directly learning the state-action mapping, IRL infers the form of reward weight and optimizes maneuvers through it. The pseudocode of maximum entropy IRL is summarized in Algorithm.1.

![IRLAlgorithm](./src/IRLAlgorithm.png)

[](<img src="https://github.com/FanGShiYuu/AWSW-PG/blob/pages/src/IRLAlgorithm.png" width="620px">)

After training, the reward weights of different driver groups can be calibrated through maximizing entropy and iterating, as shown below.

Table. IRL Calibration Results

![Calibration-Results](./src/Calibration-Results.png)

[](<img src="https://github.com/FanGShiYuu/AWSW-PG/blob/pages/src/Calibration-Results.png" width="420px">)

Table exhibits the calibration results of IRL where drivers are divided into three clusters through K-means clustering. Among them, Cluster 1 drivers exhibit the highest weight for efficiency and the lowest weight for safety and comfort, indicating a preference for high-speed overtaking rather than stopping and waiting. Hence, we classify these drivers as aggressive type. On the other hand, Cluster 2 drivers exhibit intermediate values across these three features without any distinct preferences which is named as normal type. Lastly, Cluster 3 demonstrates a predominant inclination towards safety, while displaying a relatively lower preference toward efficiency compared to the other two clusters. As a result, these drivers tend to be more cautious when making decisions, namely conservative type.

#### Experiments to assess the effectiveness of non-cooperative Bayesian game
In order to verify whether the non-cooperative Bayesian game can effectively reproduce the behavior of the human driver, we adopt the replay test method. Specifically, the subject vehicle utilizes a game theory framework consisting of six discrete strategies including turning, accelerating and decelerating for decision-making, while the interactive vehicles replays collected trajectories accordingly. 

Firstly, we conducted a comparison to assess the consistency between the reproduced sequence of passages and the actual sequence. The results reveal that 92\% of cases reproduced the same sequence of passages as observed in reality. In contrast to employing IDM as the HV decision model, it exhibits 47\% improvement. The primary reason for IDM's poor performance is that IDM is commonly employed as a car-following model, depicting the relationship between the following vehicle and the leading vehicle. Therefore, it is difficult to describe the interdependence of actions during the interaction process. The above results demonstrate that the proposed method outperforms IDM in reproducing HV decisions, thereby facilitating the establishment of a more realistic simulation environment for examining our CAV algorithm. 

Secondly, we also visualized the reproduced results in Figure, where the gray lines represent human drivers' interaction trajectories from the original data, and the blue lines represent the trajectories generated using the proposed non-cooperative Bayesian game. It can be readily discerned that our proposed game-based method can effectively reproduce diverse human driver trajectories.

![replay](./src/BNE-replay-simulation.png)

[](<img src="https://github.com/FanGShiYuu/AWSW-PG/blob/pages/src/BNE-replay-simulation.png" width="720px">)

Furthermore, the Dynamic Time Warping (DTW) algorithm is used to measure the similarities between two time-series datasets, which can be viewed as an optimization problem that minimizes the effects of shifting and distortion in time through a flexible transformation/mapping of time series [Taylor-TR-B-2015](https://www.sciencedirect.com/science/article/pii/S0191261514002264). We selected two cases in total for example, the first case is the case in which the result of the reproduced decision is consistent with the actual result.

![DTW_Case1](./src/DTW_Case1.png)

[](<img src="https://github.com/FanGShiYuu/AWSW-PG/blob/pages/src/DTW_Case1.png" width="700px">)

It is not difficult to find that only a relatively small part of the trajectory has been offset, and we use the blue dashed line to represent the DTW calculated correspondence between the two trajectories. At the same time, we choose a case where the reproduced decision is inconsistent with the actual decision. It can be seen that there are obvious differences in trajectories at this time, but since such cases account for a small proportion. Therefore, we believe that the proposed method can better reproduce the trajectories of heterogeneous human drivers. For more data, [DTW-Figs](https://github.com/FanGShiYuu/AWSW-PG/tree/pages/src/DTW-Figs) shows other DTW outputs.

![DTW_Case33](./src/DTW_Case33.png)

[](<img src="https://github.com/FanGShiYuu/AWSW-PG/blob/pages/src/DTW_Case33.png" width="700px">)

### 2.Comparison with reservation-based method
The reservation-based approach is well known for its clear principles and safety guarantees. The reservation-based system consists of an intersection manager and vehicle agents. Each approaching vehicle agent sends a request to the intersection manager to reserve certain space-time within the intersection in its desired path. In the reservation-based approach, the service sequence of the vehicles is determined under simple but effective policies such as first come first served (FCFS) strategy, auction strategy, and BATCH strategy. However, the reservation-based approach often relies on deploying all vehicle agents under the control of a coordinator. In real-world mixed driving environments where HDVs are uncontrolled, this can lead to sub-optimal performance of the reservation-based approach. Therefore, we further compare the improved Depth First Search Tree (iDFST) method, a bulk right-of-way allocation approach combining vehicle conflict topology and tree search, with the potential game proposed in this manuscript (ASWS-PG(T=8)). The performance of two models are summarized as below.

Table. Model performance comparison between iDFST and Potential game

![Model-comparison](./src/Model-comparison.png)

[](<img src="https://github.com/FanGShiYuu/AWSW-PG/blob/pages/src/Model-comparison.png" width="620px">)

According to Table, the iDFST approach fails to effectively avoid collisions for each permeability. This can be attributed to the fact that in our studied mixed environment, HDVs are not connected vehicles and possess their own decision logic, thus disregarding commands from the coordinator like CAVs. To address this issue, we propose a cooperative framework that combines adaptive methods and game theory. This framework successfully identifies heterogeneous drivers within the environment and devises targeted adjustment strategies to prevent collisions. In addition, in comparison with the proposed method, the success rate and average simulation time exhibit relatively consistent performance across different permeability levels. However, as the permeability increases, the efficiency of the cooperative system controlled by iDFST approach noticeably declines. This phenomenon can be attributed to an increasing number of agents responding to the coordinator's control, resulting in a significant increase in tree depth (passing order). Given that the iDFST approach was designed to restrict entry into the intersection to right-of-way vehicles only, resulting in an increase in travel time.

In conclusion, the current reservation-based approach is difficult to extend directly to the mixed driving environment. At the same time, our research focuses on the motion planning layer of autonomous driving, and ensuring safety and convergence in a highly uncertain environment necessitates coordinated work from multiple layers. Currently, our algorithm has only been validated in a simulation environment; however, we aim to further validate it through multidimensional approaches such as hardware-in-the-loop and driver-in-the-loop testing in future. 

### 3.Performance changes after introducing comfort
Typically, there are two predominant approaches: incorporating jerk into the overall reward as a comfort penalty or constraining deceleration within a specific range, known as comfort deceleration. Given that the decision variable of the proposed cooperative framework is acceleration, we have opted for the latter approach. Specifically, we have designed lower limits of acceleration at -4m/s2 which aligns with commonly utilized values in many car-following models and simulation settings. Meanwhile, considering the reviewer's suggestion, we also include comfort as part of the overall reward. Specifically, comfort is characterized by jerk. 

After introducing comfort into the overall reward, the regularities observed in Section 6.3 remain. Therefore, we have only updated the corresponding tables and figures in the revised manuscript. However, through a comparative analysis of the indicators before and after the introduction of comfort, small changes can be found.

Here, we would like to point out the reasons driving these changes in order to enhance reviewers' comprehension of how the integration of comfort has impacted our model. 

Table. Changes after introducing comfort

![Changes-introducingcomfort](./src/Changes-introducingcomfort.png)

[](<img src="https://github.com/FanGShiYuu/AWSW-PG/blob/pages/src/Changes-introducingcomfort.png" width="620px">)

Table exhibits the changes after introducing comfort into our overall reward. According to the Table, it is not difficult to find that the collision rate decreases under basically each permeability after introducing comfort, especially the baseline method. We posit that this phenomenon can be attributed to the discomfort caused by abrupt changes in CAV acceleration. Therefore, rapid acceleration or rapid deceleration behavior is eliminated, thereby enhancing the comprehensibility of CAV action and ultimately improving safety levels. At the same time, we found that the efficiency of the system did not change significantly (although the success rate did change, the average simulation time actually changed very little). In conclusion, the introduction of comfort has a significant improvement on system safety with no harm to system efficiency. Therefore, the enhancement of comfort is therefore deemed highly imperative, and we would like to express our gratitude once again for the reviewer’s valuable suggestions.


### 4. Continuous simulation case
A continuous flow traffic environment is of great significance for evaluating the proposed algorithm. However, due to the inclusion of both CAV and uncontrolled HDV in our simulation environment, simulation may shut down at anytime due to potential collisions. Moreover, evaluating under continuous flow simulations poses challenges as it can lead to variations not only in termination time but also in terms of vehicle number and types within the environment. As a result, there are too many variables to evaluate the model effectively. Therefore, we lean towards conducting piecemeal simulations instead.

However, the proposed cooperative driving framework is also available for a simulation environment of continuous flow. A video case is shown below.

<div align=center>
| <video muted controls width=380> <source src="./src/Continuous-simulation.mp4"  type="video/mp4"> </video> |
</div>

The purple dots in the video represent the real-time location of the vehicles, and the gray dots represent the planned trajectory. As you can see, the proposed potential game-based framework can be well applied to continuous flow simulation, and there is another interesting phenomenon. When an entrance has a larger number of vehicles, other vehicles that collide with them are more likely to slow down and give way. The reason behind this is that the system is most efficient when it is optimized to give priority to entrances with more vehicles.

## Contact

If you have any questions, feel free to contact us (2111219@tongji.edu.cn).
