# Back to the front end!

I want to do this in clojurescript with the blessJs framework for in-terminal display, but due to work requiring me to learn angular think that it would be a better use of time to learn angular and only come back to ClojureScript if I still feel motivated to do that.

## Additions to server

Added a couple useful routes for grabbing data.

* Grab file by file name, service, and environment
* Grab all files in environment
* Grab environments available
* Grab all versions of file across environments

Using the 4 of these, I believe that all combinations of operations should be doable. Essentially the plan is to list available options in the terminal.

## From here its angular time!

Reason behind this is primarily that I want to learn angular for work, and it seems like its a quite nice framework. ClojureScript + Reagent works well, but why not use a language + framework built around HTML.

## The heroes tutorial was phenomonel!

Worked through the entirety of https://angular.io/tutorial and it took me from 0 to fully feeling competent in around 4-6 hours of coding. Real real nice!

## Getting the yaml-config-manager front-end up and running

`ng new yaml-config-manager-ng`

tailwindcss.com/docs/guides/angular

## Putting together basic template

Screens that I know will be needed.

Operations header:
* View
* Apply
* Migrate

Files exploration screen:
* Should be able to show all files across all environments
* Files should be selectable
* Environments should be selectable

File-info component:
* Should list name, environment, service, path

Properites list component:
* Should list all properties for file-info
* Should allow property under review to be highlighted

Comparison screen:
* Should be able to show properties across two files.

Apply screen:
* Should take input JSON string and apply its contents to selected file

## First thing to target

Getting a flie-info to be selected and displayed.

Generating all the components up front so I can just fill in the blanks and conenct them. Will play around more to see if this was optimal

## Continued

I'll be continuing the angular blogging in LearningAngular
