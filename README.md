# repository-dashboard-shiny
Shiny application to monitor Github Issues

# Requirements #
To personalize this shiny dashboard, the following changes are required:
### Github repo configuration
* Create the following [custom Github labels] (https://help.github.com/articles/creating-and-editing-labels-for-issues-and-pull-requests/) - that all share a unique hexidecimal color (herein refered to as the SIZE label):
	* 2hr
	* 4hr
	* 1day
	* 2day
* Use any combination of these labels to estimate the size of the task - the dashboard will look for these labels by unique hexidecimal color and weight the metrics accordingly.
* The dashboard also expects all issues to be tagged with milestones that enumerate the sprint involved. The nomenclature for this milestone is "sprint x" - where x is an ordinal integer.
* Additionally, the dashboard will look for labels with the term "duplicate". If an issue has a duplicate tag, it will be removed from consideration.

### global.R configuration
* Modify the repoURL variable to point to the new repo
* Modify the sprintDeadlines variable so that:
	* The first element of the array is the begin date
	* Every date thereafter references the end of the ordinal sprint
* Modify the sizeColor variable to match the hexidecimal color for the SIZE label in the github repo.
* For users who wish to connect with a [personal access token] (https://github.com/blog/1509-personal-api-tokens), create a .Rprofile file with the following syntax (substitute xxx for your personal access token):
```
pacToken <- 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
```

# Installation and Deployment #
```
git clone https://github.com/Duke-Translational-Bioinformatics/repository-dashboard-shiny.git
```
Using Docker to deploy:
```
docker build -t benneely/dashboard:1.0
```
This image is built from the [rocker/shiny] (https://registry.hub.docker.com/u/rocker/shiny/) image, please see that page for additional deployment configurations. One possible strategy:
```
docker run --rm -p 3838:3838 benneely/dashboard:1.0
```
Once complete, the app should be available via [http://localhost:3838/dashboard/] (http://localhost:3838/dashboard/)

# Features #
Utilizes the Github API v3 to extract issues and create a dashboard that contains the following visualizations:
* Backlog Burndown
	* Displays all Github issues and assigns a size value of 1 if missing.
* Sprint Burndown
	* Displays "sprint-specific" burndown. 
	* Only includes issues with a milestone of "sprint x". 
	* If a SIZE label is missing, a value of '1' is assigned.
* Velocity Overlay
	* Average Velocity is calculated as: sum(closed tickets) / weekdays since beginning of sprint
	* Required Velocity is calculated as: sum (open tickets) / weekdays left in sprint
* Estimated Ship Date Indicator
	* Estimated as: Today + ceiling(sum(open tickets) / Average Velocity) weekdays
