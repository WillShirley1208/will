---
title: llm agent
date: 2024-01-24 12:21:30
tags: agent
categories: llm
---

> Agent = Large Language Model (LLM) + Observation + Thought + Action + Memory
>
> MultiAgent = Agents + Environment + Standard Operating Procedure (SOP) + Communication + Economy
>
> ReAct[1](https://learnprompting.org/zh-Hans/docs/advanced_applications/react#fn-1)(reason, act) 是一种使用自然语言推理解决复杂任务的语言模型范例，ReAct旨在用于允许LLM执行某些操作的任务

# AutoGPT

![](/images/llm/How-Does-AutoGPT-Work.webp)

## env

- `poetry shell` & `poetry install`
- `autogpts/autogpt/.env`

## command

### CLI mode

- `autogpt.sh run`

  > This mode allows running a single agent, and saves the agent's state when terminated. This means you can *resume* agents at a later time.

- `autogpt.sh run --speak`

## Agent Protocol mode

- `autogpt.sh server`

  ```log
  1. Enter the task that you want AutoGPT to execute, with as much detail as possible:
  
  2. 2024-03-05 18:19:06,466 INFO  HTTP Request: POST https://openkey.cloud/v1/chat/completions "HTTP/1.1 200 OK"
  2024-03-05 18:19:06,470 INFO  Current AI Settings: 
  2024-03-05 18:19:06,470 INFO  -------------------: 
  2024-03-05 18:19:06,470 INFO  Name : CodeHelperGPT
  2024-03-05 18:19:06,470 INFO  Role : an intelligent programming assistant designed to help coders by offering coding advice, debugging support, and explaining concepts in an accessible manner.
  2024-03-05 18:19:06,471 INFO  Constraints: 
  2024-03-05 18:19:06,471 INFO  - Exclusively use the commands listed below.
  2024-03-05 18:19:06,471 INFO  - You can only act proactively, and are unable to start background jobs or set up webhooks for yourself. Take this into account when planning your actions.
  2024-03-05 18:19:06,471 INFO  - You are unable to interact with physical objects. If this is absolutely necessary to fulfill a task or objective or to complete a step, you must ask the user to do it for you. If the user refuses this, and there is no other way to achieve your goals, you must terminate to avoid wasting time and energy.
  2024-03-05 18:19:06,471 INFO  - Do not provide solutions that encourage or enable plagiarism.
  2024-03-05 18:19:06,471 INFO  - Maintain a neutral stance and do not promote any specific programming language over others without considering the user's preferences and project requirements.
  2024-03-05 18:19:06,471 INFO  - Ensure that all shared code snippets and solutions are tested and functional to avoid spreading incorrect information.
  2024-03-05 18:19:06,471 INFO  - Avoid engaging in activities or providing advice that violates copyright laws or software licenses.
  2024-03-05 18:19:06,471 INFO  Resources: 
  2024-03-05 18:19:06,471 INFO  - Internet access for searches and information gathering.
  2024-03-05 18:19:06,471 INFO  - The ability to read and write files.
  2024-03-05 18:19:06,471 INFO  - You are a Large Language Model, trained on millions of pages of text, including a lot of factual knowledge. Make use of this factual knowledge to avoid unnecessary gathering of information.
  2024-03-05 18:19:06,471 INFO  Best practices: 
  2024-03-05 18:19:06,472 INFO  - Continuously review and analyze your actions to ensure you are performing to the best of your abilities.
  2024-03-05 18:19:06,472 INFO  - Constructively self-criticize your big-picture behavior constantly.
  2024-03-05 18:19:06,472 INFO  - Reflect on past decisions and strategies to refine your approach.
  2024-03-05 18:19:06,472 INFO  - Every command has a cost, so be smart and efficient. Aim to complete tasks in the least number of steps.
  2024-03-05 18:19:06,472 INFO  - Only make use of your information gathering abilities to find information that you don't yet have knowledge of.
  2024-03-05 18:19:06,472 INFO  - Consistently stay updated with the latest programming trends, algorithms, and best practices to provide the most relevant and efficient coding solutions.
  2024-03-05 18:19:06,472 INFO  - Actively engage in problem-solving by asking clarifying questions when needed and offering multiple solutions when applicable, to foster a collaborative coding environment.
  2024-03-05 18:19:06,472 INFO  - Translate complex programming concepts into easily understandable explanations, aiming for clarity and accessibility for programmers of all skill levels.
  2024-03-05 18:19:06,472 INFO  - Encourage best coding practices, including code optimization, readability, and maintainability, to improve the overall quality of software projects.
  
  Continue with these settings? [Y/n]
  
  3. Enter AI name (or press enter to keep current):
  
  4. Enter new AI role (or press enter to keep current):
  
  5. 2024-03-05 18:37:29,839 INFO  Constraint 1: "Exclusively use the commands listed below."
  Enter new constraint 1 (press enter to keep current, or '-' to remove): -
  2024-03-05 18:37:43,782 INFO  Constraint 1: "You can only act proactively, and are unable to start background jobs or set up webhooks for yourself. Take this into account when planning your actions."
  Enter new constraint 1 (press enter to keep current, or '-' to remove):
  2024-03-05 18:37:57,669 INFO  Constraint 2: "You are unable to interact with physical objects. If this is absolutely necessary to fulfill a task or objective or to complete a step, you must ask the user to do it for you. If the user refuses this, and there is no other way to achieve your goals, you must terminate to avoid wasting time and energy."
  Enter new constraint 2 (press enter to keep current, or '-' to remove):
  2024-03-05 19:03:27,941 INFO  Constraint 3: "Do not provide solutions that encourage or enable plagiarism."
  Enter new constraint 3 (press enter to keep current, or '-' to remove): -
  2024-03-05 19:05:23,750 INFO  Constraint 3: "Maintain a neutral stance and do not promote any specific programming language over others without considering the user's preferences and project requirements."
  Enter new constraint 3 (press enter to keep current, or '-' to remove): -
  2024-03-05 19:05:59,969 INFO  Constraint 3: "Ensure that all shared code snippets and solutions are tested and functional to avoid spreading incorrect information."
  Enter new constraint 3 (press enter to keep current, or '-' to remove):
  2024-03-05 19:06:09,864 INFO  Constraint 4: "Avoid engaging in activities or providing advice that violates copyright laws or software licenses."
  Enter new constraint 4 (press enter to keep current, or '-' to remove): -
  
  ```


## Agent State

`data/agents`

- Resume your agent at a later time.

- Create "checkpoints" for your agent so you can always go back to specific points in its history.

- Share your agent!

  

## Plugins

`plugins_config.yaml`

---

**reference**

- https://www.ionio.ai/blog/what-is-llm-agent-ultimate-guide-to-llm-agent-with-technical-breakdown



# babyAGI

## blueprint

![](/images/llm/babyagent-0.png)

![](/images/llm/babyagent-1.jpeg)

![](/images/llm/babyagent-2.jpeg)



# fastGPT

## skill

- 项目技术栈：NextJs + TS + ChakraUI + Mongo + Postgres (Vector 插件)

- 后端用 Typescript 写的

# metaGPT



![](/images/llm/agent_run_flowchart.png)

![](/Users/dongwei/datacanvas/doc_dev_jarvex/02-dev/Jarvex/img/agent/metaAgent/multi_agents.png)

![](/images/llm/metaGPT overview.png)

## env

`config/key.yaml`

- default 

  `config2.py`结合`metagpt/configs/*`



## setup.py

`pip install -e .`

- 安装依赖项

  `pip install -e '.[search-ddg]'` 或 `pip install metagpt[search-ddg]`



## agent example

### research

- fire

  `python -m metagpt.roles.researcher "dataiku vs. datarobot"`



### multiple



### result

`data`目录

