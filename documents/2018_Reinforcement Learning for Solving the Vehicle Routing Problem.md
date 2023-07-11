# Reinforcement Learning for Solving the Vehicle Routing Problem

## introduction

- 组合优化问题：VRP（也可用于其他组合优化问题，如：TSP、背包问题
- 基于“NEURAL COMBINATORIAL OPTIMIZATION WITH REINFORCEMENT LEARNING（2017）”

## model

- encode不使用pointer network，而是使用embedding layer，减少了计算复杂度，且性能不会降低
- decode还是pointer network
- 输入分为static和dynamic两部分，只有static使用encode进行embedding，dynamic只输入attention layer（按理说dynamic也应该进行encode的，但实验效果不明显）