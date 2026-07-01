# CHAD
CHAD: A Scalable Turn-Based Simulator for Complex Heterogeneous Agent Dynamics in Financial Markets


 # CHAD: Complex Heterogeneous Agent Dynamics                                                                                                                                                              
                                                                                                                                                                                                           
 CHAD is a high-performance, Julia-based Agent-Based Model (ABM) designed to simulate financial market microstructure. Unlike event-driven frameworks that rely on asynchronous queues, CHAD employs a     
 **pre-scheduled, turn-based execution architecture**. This ensures deterministic behavior and enables the simulation to scale to millions of agents with near-linear performance.                         
                                                                                                                                                                                                           
 CHAD generates realistic market dynamics—reproducing key **stylized facts**—strictly through endogenous agent interactions, without the need for exogenous price guidance or historical replay.           
                                                                                                                                                                                                           
 ---                                                                                                                                                                                                       
                                                                                                                                                                                                           
 ## ⚠️ Important Note: Functional Swap of Orders                                                                                                                                                           
 **Please be aware:** In the current implementation, the logical handling of buy and sell orders is functionally swapped.                                                                                  
 *   Code referring to `asks` may be processing buy order functions.                                                                                                                                       
 *   Code referring to `bids` may be processing sell order functions.                                                                                                                                      
 This is a known structural quirk in the codebase. If you are extending the order-matching engine or performing manual order submission, **you must account for this inversion.** A refactor to align the  
 logic with standard market conventions is recommended for future development.                                                                                                                             
                                                                                                                                                                                                           
 ---                                                                                                                                                                                                       
                                                                                                                                                                                                           
 ## Core Contributions                                                                                                                                                                                     
                                                                                                                                                                                                           
 CHAD introduces three principal design contributions to the field of market microstructure modeling:                                                                                                      
                                                                                                                                                                                                           
 1.  **Pure Endogenous Realism:** The model generates complex macroscopic dynamics (fat-tailed distributions, volatility clustering) emergent solely from the interaction of heterogeneous agents.         
 2.  **Dual-Book Architecture:** The framework separates the public **Limit Order Book (LOB)** from a private **Stop-Loss Order Book (SLOB)**. This allows for explicit modeling of latent liquidity,      
 recursive liquidation cascades, and systemic fragility.                                                                                                                                                   
 3.  **Deterministic Scalability:** By pre-computing a master schedule of all agent activation times, CHAD eliminates the computational overhead of dynamic event queues, enabling near-linear scaling to  
 over 1,000,000 agents.                                                                                                                                                                                    
                                                                                                                                                                                                           
 ---                                                                                                                                                                                                       
                                                                                                                                                                                                           
 ## System Architecture                                                                                                                                                                                    
                                                                                                                                                                                                           
 ### The Dual-Book System                                                                                                                                                                                  
 The simulation engine manages two distinct order structures:                                                                                                                                              
 *   **Public LOB:** Stores visible liquidity (asks/bids) used for active price discovery.                                                                                                                 
 *   **Private SLOB:** Stores latent, trigger-based liquidity (stop-loss orders). These orders are evaluated continuously against the most recent transaction price. Upon trigger, they convert into       
 market orders, potentially initiating recursive liquidity cascades.                                                                                                                                       
                                                                                                                                                                                                           
 ### Execution Loop                                                                                                                                                                                        
 Instead of dynamic event handling, CHAD uses a pre-calculated master schedule. When an agent is triggered at tick $t_i$, it undergoes a four-step lifecycle:                                              
 1.  **Perception:** Querying global state ($P_{t-1}^{\text{last}}$).                                                                                                                                      
 2.  **Evaluation:** Processing archetype-specific logic.                                                                                                                                                  
 3.  **Execution Formulation:** Creating the `Order` struct.                                                                                                                                               
 4.  **Submission:** Sending the order to the core matching engine.                                                                                                                                        
                                                                                                                                                                                                           
 ---                                                                                                                                                                                                       
                                                                                                                                                                                                           
 ## Installation                                                                                                                                                                                           
                                                                                                                                                                                                           
 This project requires [Julia](https://julialang.org/). Install the necessary dependencies by running the following command in your Julia REPL:                                                            
                                                                                                                                                                                                           
 ```julia                                                                                                                                                                                                  
 using Pkg                                                                                                                                                                                                 
 Pkg.add(["Random", "Distributions", "ProgressBars", "DataFrames", "Statistics", "DelimitedFiles", "CSV", "Serialization", "Measures", "DataStructures", "UUIDs", "StatsBase", "Plots", "Dates", "Printf", 
 "PlotlyJS"])                                                                                                                                                                                              
 ```                                                                                                                                                                                                       
                                                                                                                                                                                                           
 ---                                                                                                                                                                                                       
                                                                                                                                                                                                           
 ## Running the Simulation                                                                                                                                                                                 
                                                                                                                                                                                                           
 1.  **Configure Scenarios:** Modify the agent ecology, stop-loss probabilities, and look-back horizons within `MAIN.jl`.                                                                                  
 2.  **Execute:** Run the main simulation loop:                                                                                                                                                            
     ```bash                                                                                                                                                                                               
     julia MAIN.jl                                                                                                                                                                                         
     ```                                                                                                                                                                                                   
 3.  **Output:** Simulation logs, including stylized facts metrics and error scores, are saved to the `/results` directory as `Simulation_Results.csv`.                                                    
                                                                                                                                                                                                           
 ---                                                                                                                                                                                                       
                                                                                                                                                                                                           
 ## Validation & Research Context                                                                                                                                                                          
 CHAD is validated against empirical BTC/USDT market data using a **Validity Corridor** methodology. The model’s robustness has been tested across a broad stress-test space, spanning from low-friction   
 conditions to highly reactive, stop-loss-dense regimes.                                                                                                                                                   
                                                                                                                                                                                                           
 For a deep dive into the methodology, performance benchmarks, and the mathematical definitions of agent archetypes (Zero-Intelligence, Trend-Following, Mean-Reverting, and Market Makers), please refer  
 to our associated paper:                                                                                                                                                                                  
 > *CHAD: A Scalable Turn-Based Simulator for Complex Heterogeneous Agent Dynamics in Financial Markets* (MABS 2026 Workshop).                                                                             
                                                                                                                                                                                                           
 ---                                                                                                                                                                                                       
                                                                                                                                                                                                           
 ## Folder Structure                                                                                                                                                                                       
 *   `MAIN.jl`: Main orchestration and configuration.                                                                                                                                                      
 *   `CHAD_Functions.jl`: Core LOB, SLOB, and market engine logic.                                                                                                                                         
 *   `Agent_functions.jl`: Agent definitions and behavioral archetypes.                                                                                                                                    
 *   `/results/`: Output logs and calibration data.                                                                                                                                                        
                                                                                                                                                                                                           
 ---                                                                                                                                                                                                       
 GitHub Repository: https://github.com/patrick-liston/CHAD        
