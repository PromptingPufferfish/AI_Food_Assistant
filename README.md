# AI Food Assistant
A free AI Food Assistant based on OpenClaw. From Paprika App recipes to meal plan to Google Keep grocery list. 

# Setup

## 1. Install Linux system
Install full-blown Ubuntu Linux system on your machine or - as a Windows user - setup an Ubuntu distribution via Windows Subsystem for Linux:
- Exectue cmd.exe under Windows with admin rights
- Install Ubuntu:
```bash
wsl --install -d Ubuntu
```
- Set user and password

## 2. Install OpenClaw
- In the Ubuntu shell enter:
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```
- Follow OpenClaw installation procedure, configure LLM and channel (messenger bridge)
- Finalize the OpenClaw installation - init, update, install ClawHub:
```bash
newgrp
openclaw update
openclaw gateway start
openclaw dashboard
sudo npm install -g clawhub
```

## 3. Install & use gkeep-notes skill 

As described in [PromptingPufferfish/gkeep-notes/blob/main/README.md](https://github.com/PromptingPufferfish/gkeep-notes/blob/main/README.md)

## 4. Install paprika skill
### 4a. Installation via clawhub.ai 
Install paprika skill from ClawHub ([Link to paprika skill on ClawHub](https://clawhub.ai/mjrussell/paprika))
```bash
clawhub install paprika
```

### 4b. Add credentials 
Set environment variables in SKILL.md:
```bash
export PAPRIKA_EMAIL="your.email.goes.here@gmail.com"
export PAPRIKA_PASSWORD="your.password"
```

### 4c. Init & consistency check
Restart, then check if skill appears as "ready" in the list:
```bash
openclaw gateway restart
openclaw skills list --eligible
```

### 4d. Make OpenClaw aware of the new skill
Via local OpenClaw chat as described in 3b: 
```bash
"How many recipes do I have in my Paprika App? Use your paprika skill. Read ~/.openclaw/workspace/skills/paprika/SKILL.md for an overview. Environment variables for authentication are set."
"Show a list of all recipes from paprika with title, preperation instructions and ingredients including metric quantities per each ingredient. do not skip recipes, show all recipes stored in paprika."
"Ok, that worked! Connection to paprika is now established. Please remember how you did that. Update /.openclaw/workspace/MEMORY.md long-term memory now with this information. Do not overwrite exising content in MEMORY.md, only add information. Tell me when you are done."
```

If you want to speed things up then here is a template from a pre-trained OpenClaw instance for you: [https://github.com/PromptingPufferfish/AI-Food-Assistant/blob/main/MEMORY.md-paprika](https://github.com/PromptingPufferfish/AI-Food-Assistant/blob/main/MEMORY.md-paprika). 

## 5. Add meal planning functionality
Can be added as a skill or as general knowledge via MEMORY.md

### 5a. Add as a skill
Existing meal-planner skills work with own recipe, meal plan and shopping list storages - usually local files in /.openclaw/memory/. If you want to develop your own skill I recommend starting with an existing one, e. g. [https://clawhub.ai/ivangdavila/meal-planner](https://clawhub.ai/ivangdavila/meal-planner). Add some additional instructions: recipes stored in ~/meal-planner/recipes/ are created from paprika skill output, shopping lists created in ~/meal-planner/shopping/ are transfered to Google Keep via gkeep-notes skill. No programming skills required, describe in natural language.

### 5b. Add as general knowledge via MEMORY.md
You can also train your OpenClaw via chat:
```bash
Please remember the following recurring process to create a mealplan based on Paprika App recipes and add the ingredients to a Google Keep note/list. write it to MEMORY.md right now. Do not overwrite MEMORY.md, add this process only.

This is the process:

1. Ask for criteria to consider when planning a meal: which calendar week is the meal planning for how many persons, visitors yes/no and on which days, preferences like veggie or fish, allergic restrictions, etc.

2. Retrieve recipes matching the criteria using the paprika skill. the authentication is stored in the skill, do not ask for user or password. only use paprika to search for recipes, no other tools.

3. Show me the recommended mealplan, ask for adjustments. Iterate as long a I want adjustments.

4. When mealplan is confirmed then store it under /memory/MEALPLAN_YYYY-WW where YYYY-WW represents the calendar week the mealplan is scheduled for.

5. Derive all ingredients from the recipes and the quantities. use metric units like gram, kilo or litre. Create an aggregated list with ingredients and the summed quantities accross all recipes. Store the list with one ingredient + summed quantity per line. no headings or other additions. Store under /memory/SHOPPINGLIST_YYYY-WW
  where YYYY-WW represents the calendar week the mealplan related to this shopping list is scheduled for.

6. Store the ingredients in Google Keep list <shopping_list_name>. Obtain the <note_id> of the shopping list first by using gkeep search "<shopping_list_name>". 

7. Then execute skill gkeep ("gkeep add") per each line in the ITEMS* file. Create a shell script containing all the gkeep add commands for all the ingredients. Execute the shell script to ensure that items are all created within one single process. no gkeep login needed, authorization is already done in the background.

7. Give me a hint when everything is done.
```

If you want to speed things up then here is a template from a pre-trained OpenClaw instance for you: [https://github.com/PromptingPufferfish/AI-Food-Assistant/blob/main/MEMORY.md-mealplanning](https://github.com/PromptingPufferfish/AI-Food-Assistant/blob/main/MEMORY.md-mealplanning). 

**HINT: the MEMORY.md templates shared in this README file are to be used additively. Append all three of them to your existing MEMORY.md to add the whole functionality. If OpenClaw does not react on them a "That has not worked. Check your MEMORY.md file." in the chat might help.**
