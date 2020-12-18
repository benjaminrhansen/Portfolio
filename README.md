#+IMPORTANT: -*- mode: org -*-
#+title: Benjamin Hansen's Portfolio
#+language: en
#+options: H:4 num:nil toc:nil \n:nil @:t ::t |:t ^:t *:t TeX:t LaTeX:t
#+options: html-postamble:nil
#+startup: fold entitiespretty
#+setupfile: theme-bigblow.setup

* Publish                                                          :noexport:

#+begin_src elisp :results silent
  (require 'git) ; for git commands  
  ;; Benji's code to always show thie line numbers!
  (global-visual-line-mode)
  (defun commit-and-push-to-repo (&optional file-to-commit commit-message)
    (setq commit-message (or commit-message "New upload."))
    (setq file-to-commit (or file-to-commit "docs/index.html"))
    (let ((git-repo "."))
      (git-add file-to-commit)
      (git-commit commit-message file-to-commit)
      (git-fetch "origin")
      (git-push "origin" "master")))
#+end_src
#+begin_src elisp :results silent
  (defun commit-and-push-all (&optional commit-message)
    (setq commit-message (or commit-message "New Upload."))
    (let ((git-repo ".")
          (all-files-to-push '(
            "docs/index.html"
            "docs/BenjaminRHansenResume.pdf"
            "index.txt"
            "docs/theme-bigblow.setup"))
         )
      (copy-file "../Resumes/BenjaminRHansenResume.pdf" "docs/BenjaminRHansenResume.pdf" t)
      (copy-file "theme-bigblow.setup" "docs/theme-bigblow.setup" t)
      (git-add
        all-files-to-push)
      (git-commit commit-message all-files-to-push)
      (git-fetch "origin")
      (git-push "origin" "master")))
#+end_src
#+begin_src elisp
  (commit-and-push-to-repo "docs/" "Upload docs")
#+end_src

#+results:
: To github.com:benjaminrhansen/Portfolio.git
:    409d88e..5d7366a  master -> master

#+begin_src elisp :results silent
  (defun remove-all-todos-and-tags ()
    (while (sp--search-forward-regexp "TODO")
      (end-of-line)
      (org-set-tags "")
      (org-todo "")))
      (defun remove-all-noexport-on-todos ()
    (while (sp--search-forward-regexp "TODO")
      (end-of-line)
      (org-set-tags "")))
  (defun add-all-noexport-on-todos ()
    (while (sp--search-forward-regexp "TODO")
      (end-of-line)
      (org-set-tags "noexport")))
#+end_src

** Links

  [[elisp:(browse-url-of-file (org-html-export-to-html))][Export to HTML and Browse]]
  Save first
  [[elisp:(org-html-export-to-html)][Export to HTML]]
  [[elisp:(rename-file "index.html" "docs/" t)][Move to docs]]
  [[elisp:(copy-file%20"../Resumes/BenjaminRHansenResume.pdf"%20"docs/"%20t)][Copy Resume PDF to docs]]
  [[elisp:(commit-and-push-all)][Commit & Push All Possible New Files]]

  [[https://benjaminrhansen.github.io/Portfolio/]]

* About
  [[file:linkedinprofilecircle.png][file:./linkedinprofilecircle.png]]
| [[https://linkedin.com/in/benjaminrhansen][LinkedIn Profile]]  | [[file:BenjaminRHansenResume.pdf][Resume]] | [[https://github.com/benjaminrhansen][GitHub Profile]]  |
*** Self Introduction
**** TLDR?
     No problem. Here are some keywords that I feel describe me:

     *Soft Skills*
     - Quick Learner
     - Team Leader & Collaborator
     - Motivated Worker
     *Hard Skills*
     - Erlang
     - C++
     - JavaScript
     - Python
**** A Longer Intro.
**** My Beginnings


** TODO References                                                 :noexport:
*** TODO Lee Barney                                                :noexport:
*** TODO John Batty                                                :noexport:
*** TODO Others...                                                 :noexport:
* Side Projects
** Automation of Summer Workflow
  This summer I didn't work like last because of COVID-19 and my desire to prepare for my future. I had a long list of goals I wanted to accomplish and skills I wanted to refine. Here are just a few of them:

  - Prepare for Calculus II: Refine my knowledge of Calculus
  - Increase Job Skills: Learn whatever I need to learn to be more attractive to employers, like Docker, Kubernetes, and Erlang Fault-tolerance.
  - Learn Physics: Prepare myself for a future career in Quantum Information Theory.

  But I was worried. If I don't make a schedule, I won't do work on some goals because I'll get caught on others and forget to switch contexts. On the other hand, if I make my schedule and then something comes up I didn't expect, I fall behind on my goals and end up just continuing the current goal I'm on and throwing away the rest. To avoid both of those traps, I automated my workflow so each day I can specify which goals I want to work on, any miscellaneous events or goals I need to do, and for how long so that each day is customized to what is going on then. For example, I can't have a repeating schedule because I don't always start the day at the same time.

  I ended up making a series of apps written in JavaScript, the most crucial one being the Dynamic Schedule app, which allows me to use the basic interface features of my Macbook Pro and create my schedule which is then saved in my calendar. I read from a JSON my desired recurring and miscellaneous goals, and then allow the user (myself) to choose from the list of goals a subset that I want to work on. Then, one by one I can choose which to prioritize first (higher priority goals will be scheduled first). I can add miscellaneous goals and events, with custom time durations and they are automatically saved to my calendar for that day. The nice thing I also coded was keeping track of miscellaneous goal.

  Let's say I wanted to create a simple but fun Macbook Pro game, using the Script Editor (which I used for these JavaScript apps) but I ran out of time for that day to finish it. My memory is not all powerful, so there is a good chance the next day I'll forget I had that goal. The scheduling app saves all miscellaneous goals I add to a JSON so then on the next day when I go to schedule my daily plan, it will show me that I had that goal.

  All of this helped me tons in staying on track to achieving my goals from Aug. to mid Sept. of 2020.
*** DynamicSchedule.scpt
#+begin_src js
  // knowledge: all functions return something, either undefined if no return keyword
// is used, or what is returned. Use the "return" keyword! Ugh... wish it would
// just return what was last said
// Setup
var app = Application.currentApplication();
app.includeStandardAdditions = true;
// to read/write, from https://developer.apple.com/library/archive/documentation/LanguagesUtilities/Conceptual/MacAutomationScriptingGuide/ReadandWriteFiles.html
// if needed
function readFile(file) {
    // Convert the file to a string
    const fileString = file.toString();
 	//app.displayDialog(fileString);
    // Read the file and return its contents
    return app.read(Path(fileString));
}
// used for miscJSON.txt
function writeTextToFile(text, file, overwriteExistingContent) {
    try {

        // Convert the file to a string
        var fileString = file.toString();

        // Open the file for writing
        var openedFile = app.openForAccess(Path(fileString), { writePermission: true });

        // Clear the file if content should be overwritten
        if (overwriteExistingContent) {
            app.setEof(openedFile, { to: 0 });
        }

        // Write the new content to the file
        app.write(text, { to: openedFile, startingAt: app.getEof(openedFile) });

        // Close the file
        app.closeAccess(openedFile);

        // Return a boolean indicating that writing was successful
        return true;
    }
    catch (error) {

        try {
            // Close the file
            app.closeAccess(file);
        }
        catch(error) {
            // Report the error is closing failed
            console.log(`Couldn't close file: ${error}`);
        }

        // Return a boolean indicating that writing was unsuccessful
        return false;
    }
}

// simulate saving an event
function simulateSavingEvent(eventStart, duration) {
	var eventEnd = new Date(eventStart.getTime()); // make it an end date!
	// time overflows will be taken care of in the set functions
	eventEnd.setHours(eventStart.getHours() + duration.hour); // in military time
	eventEnd.setMinutes(eventStart.getMinutes() + duration.minute);
	eventEnd.setSeconds(eventStart.getSeconds() + duration.second);

	return eventEnd;
}

// saves an event to my calendar
function saveEvent(eventTitle, eventStart, duration) {
	var eventEnd = new Date(eventStart.getTime()); // make it an end date!
	// time overflows will be taken care of in the set functions
	eventEnd.setHours(eventStart.getHours() + duration.hour); // in military time
	eventEnd.setMinutes(eventStart.getMinutes() + duration.minute);
	eventEnd.setSeconds(eventStart.getSeconds() + duration.second);

	const projectCalendars = Calendar.calendars.whose({name: "benjamminhansen@gmail.com"});
	const projectCalendar = projectCalendars[0];
	const event = Calendar.Event({summary: eventTitle, startDate: eventStart, endDate: eventEnd, description: Object.values(duration).reduce((str, time) => {
		var strTime = time.toString(); // may only be one char
		// see if we need to pad the number with a zero
		if (strTime.length == 1) {
			strTime = "0" + strTime;
		}
		str += strTime + ":";
		return str;
	}, "").slice(0,-1)/* slice off last hanging ':' */});
	projectCalendar.events.push(event);
	return eventEnd; // return the end date of this event we saved
}

// generate an inclusive integer range from the first value of incrementor to the end
function range(incrementor, end, numbers = []) {
	if (incrementor > end) return numbers;
	// add a new element with the bracket operator
	numbers[numbers.length] = incrementor;
	return range(incrementor + 1, end, numbers);
}

// from https://stackoverflow.com/questions/4856717/javascript-equivalent-of-pythons-zip-function
const zip = rows => rows[0].map((_,c)=>rows.map(row=>row[c]));
// sorting function for the sort method
const sortOnPriority = (goal1, goal2)=>goal1["priority"] - goal2["priority"];

// not used
function mapInputToObjectayAndSort(desiredOrder, objectay) {
	return zip([objectay, desiredOrder]).reduce(
		(accumList, [Goal, priority])=> {
			Goal["priority"] = priority;
			accumList.push(Goal);
			return accumList
		}, []).sort(sortOnPriority);
}

// map the string of numbers to a list of actual numbers
function stringToNumberList(stringOfNumbers) {
	// delimeter must be a single comma
	return stringOfNumbers.split(",").map(StringNumber => parseInt(StringNumber));
}

// a high-order function to continue prompting the user for input while
// they "haven't submitted" the form, which will be defined in the funNotSubmitted
// lambda and called each time until it returns false, denoting the user has submitted
function untilSubmission(funGetInput, funNotSubmitted) {
	var result;
	do { result = funGetInput(); } while (funNotSubmitted(result));
	return result;
}

// generalized until construct
// allow for use of an accumulator
// said, "Until condition for accumulator is true, keep calling fun"
function until(condition, fun, accumulator) {
	// while the condition isn't true, keep iterating
	while(!condition(accumulator))
		accumulator = fun(accumulator);
	return accumulator;
}

// a fun for what is true in "not submitted" terms for
// the "chooseFromList" user interface
const chooseFromListRetry = dialogResult => dialogResult === false;
const displayDialogRetry = dialogResult => dialogResult.buttonReturned === "Retry";
const chooseFromListRestart = dialogResult => dialogResult === false;
const parseTime = timeString => { return {hour: parseInt(timeString.substring(0,2)), minute: parseInt(timeString.substring(3,5)), second: parseInt(timeString.substring(6,8))}; }

// get the first property name from an object
// not needed
// const getFirstPropertyName = obj => Object.keys(obj)[0]; // a lambda


// read from the recurringGoalsJSON.txt on the Desktop the recurring goals of
// the desired user. These will be the main goals from which the user picks
// today's specific goals
const recurringGoals = JSON.parse(app.read(Path("/Users/benjaminhansen/Desktop/recurringGoalsJSON.txt")))
	.sort(sortOnPriority);


// we will use duration times for the eventEnd date of each goal
// miscellaneous goals is dynamic, growing and shrinking each running
/*
var goal_details = [
	{ goal: "Workout: Stretches & Pushups",
	  duration: { hour: 0, minute: 6, second: 11 },
	  priority: 1 },
	{ goal: "Quantum Mechanics",
	  duration: { hour: 1, minute: 0, second: 0 },
	  priority: 2 },
	{ goal: "Calculus",
	  duration: { hour: 1, minute: 0, second: 0 },
	  priority: 3 },
	{ goal: "Algorithms and Complexity",
	  duration: { hour: 0, minute: 15, second: 0 },
	  priority: 4 },
	{ goal: "Course Hero",
	  duration: { hour: 0, minute: 45, second: 0 },
	  priority: 5 },
	{ goal: "Find 7 New Resources",
	  duration: { hour: 1, minute: 0, second: 0 },
	  priority: 6 },
	{ goal: "Make 5 Contacts",
      duration: { hour: 1, minute: 30, second: 0 },
	  priority: 7 },
	{ goal: "Setup 1 Meeting",
	  duration: { hour: 0, minute: 15, second: 0 },
	  priority: 8 },
	{ goal: "Increase Job Skills",
	  duration: { hour: 1, minute: 0, second: 0 },
	  priority: 9 },
	{ goal: "Build Portfolio Up",
	  duration: {hour: 1, minute: 0, second: 0 },
	  priority: 10 },
	{ goal: "Linear Algebra",
	  duration: {hour: 1, minute: 0, second: 0 },
	  priority:11 }
];*/

var Calendar = Application("Calendar");

// read in the miscellaneous goals JSON file
// parse the miscelleneous goals from the file
// the JSON contains an ay of goals. Thus we could have more than one goal
// sort the list based on priority
const misc = JSON.parse(app.read(Path("/Users/benjaminhansen/Desktop/miscJSON.txt")))
	.sort(sortOnPriority);
//app.displayDialog("Misc Sorted Goals: " + JSON.stringify(misc));
// ask if previous miscelleneous tasks have been completed
const completedMiscGoals =
	untilSubmission(function() {
		return app.chooseFromList(misc.map(Goal => Goal.goal), {
			withTitle: "It's Your Choice",
			withPrompt: "Which misc. goals have you completed?",
			defaultItems: [],
			multipleSelectionsAllowed: true,
			emptySelectionAllowed: true,
			okButtonName: "Submit",
			cancelButtonName: "Retry"
		});
	}, chooseFromListRetry);

// does the user have any misc goals to add
const hasMiscGoalsToAdd = app.displayDialog("Do you have any misc. goals to add?", {
	withTitle: "Misc. Goals",
	buttons: ["No","Yes"],
	defaultButton: "Yes"
}).buttonReturned == "Yes";

// may be empty. if the user hasMiscGoalsToAdd, then call the until function, else return the empty list
const newMiscGoals =  hasMiscGoalsToAdd ?
	// until the user is done creating misc. goals to work on, keep prompting
	until(([_m, _p, lastGoalDialogSubmission]) => lastGoalDialogSubmission.buttonReturned === "I'm Done",
		// the main function
		([miscGoals, priority, _]) => {
			// two prompts. 1: What is the goal name to attach priority to
			// 				2: What is the time duration of the goal (hh:mm:ss)

			// prompt 1 for the goal name
			const goalName = untilSubmission(() => app.displayDialog("Enter Goal Title", {
				withTitle: "New Misc. Goal",
				defaultAnswer: "",
				withIcon: "note",
				buttons: ["Retry", "Next"],
				defaultButton: "Next"
			}), displayDialogRetry).textReturned;

			// prompt 2 for the goal's time duration (hh:mm:ss)
			// parseTime parses text of the form (hh:mm:ss) and returns an object
			// of the form {hour: hh, minute: mm, second: ss}
			// gross code to create a lambda and call it!
			const func = () => {
				const tmpResponse = untilSubmission(() => app.displayDialog(`Enter Time Duration for ${goalName} (in hh:mm:ss format):`, {
					withTitle: "New Misc. Goal",
					defaultAnswer: "hh:mm:ss",
					withIcon: "note",
					buttons: ["I'm Done", "Retry", "Create Another"],
					defaultButton: "Create Another"
				}), displayDialogRetry);
				// parse the time given by the text response
				const tmpTime = parseTime(tmpResponse.textReturned);
				return [tmpTime, tmpResponse];
			}
			[goalDuration, goalDurationPromptResult] = func();

			// add to the list the new goal
			miscGoals.push({goal: goalName, duration: goalDuration, priority: priority});

			// return the updated list, an increasing priority, and the submission
			// of the goalDurationPromptResult
			return [miscGoals, priority + 1, goalDurationPromptResult];
		}, [[], 1, {buttonReturned: ""} /* for compatability reasons */])[0] : []; // return the accumulated list, or if no goals to add, the empty list

//app.displayDialog(`New Misc. Goals: ${JSON.stringify(newMiscGoals)}`);
// filter the goal_details.misc accordingly
const miscGoalsToSave = misc.filter(function(Goal) {
	// return true if the Goal's goal title is not in the completedMiscGoals
	return !completedMiscGoals.includes(Goal.goal);
}).concat(newMiscGoals); // add the new goals the user entered

// let me define the break between each event
const breakTime = 0; // make this functional

// Keep prompting until satisfied
// define the not submitted function to be false if the return of the
// first function returned false. False for a chooseFromList denotes the user
// cancelled with the cancelButton
const todaysMainGoalChoices =
	untilSubmission(function() {
		return app.chooseFromList(recurringGoals.map(Goal => Goal.goal), {
			withTitle: "It's Your Choice",
    		withPrompt: "Select the recurring goals you want to work on today:",
    		defaultItems: [],
			multipleSelectionsAllowed: true,
			emptySelectionAllowed: true,
			okButtonName: "Submit",
			cancelButtonName: "Retry"
		});
	}, chooseFromListRetry);

// filter the main goals to the one's I want to do today
const todaysMainGoals = recurringGoals.filter(Goal => todaysMainGoalChoices.includes(Goal.goal));


const miscGoalChoices =
	untilSubmission(function() {
		return app.chooseFromList(miscGoalsToSave.map(Goal => Goal.goal), {
			withTitle: "It's Your Choice",
			withPrompt: "Select the misc. goals you want to work on today:",
			defaultItems: [],
			multipleSelectionsAllowed: true,
			emptySelectionAllowed: true,
			okButtonName: "Submit",
			cancelButtonName: "Retry"
		});
	}, chooseFromListRetry);

// maintain a miscGoals array of objects
// set priorities for these goals starting at the length of today's main goals
// + 1
const todaysMiscGoals = miscGoalsToSave.filter(Goal => miscGoalChoices.includes(Goal.goal)).reduce(
		([prioritizedGoals, priority], Goal) => {
			Goal["priority"] = priority;
			prioritizedGoals.push(Goal);
			return [prioritizedGoals, priority + 1];
}, [[], todaysMainGoals.length + 1])[0]; // return first element of list

// Now, finally determine all the goals I want to work on.
const todaysGoals = todaysMainGoals.concat(todaysMiscGoals);

// if the goals are emtpy, don't continue
// we can indent this later...
if (todaysGoals.length) {

	// now all the goals are ordered, but what if the user wants a different duration
	// for some of the goals, because not all goals will take the same amount of time
	// get a selection of goals the user wants to update durations for
	const durationsToUpdate = untilSubmission(() =>
		// choose from the list of currently ordered goals
		app.chooseFromList(todaysGoals.map(Goal => Goal["goal"]), {
			withTitle: "Not the Usual?",
			withPrompt: `Which goals for today would you like to enter a different time duration for?`,
			defaultItems: [],
			multipleSelectionsAllowed: true,
			emptySelectionAllowed: true,
			okButtonName: "Submit",
			cancelButtonName: "Retry"
		}) /*end first arg*/, chooseFromListRetry);

	const todaysUpdatedGoals = durationsToUpdate.length !== 0 ? // make sure we have goals to update
		// reduce on the durationsToUpdate list of goal names
		// start the accumulator of the updating goals with the todaysOrderedGoal
		// list. Update each goal corresponding to the name in durationsToUpdate
		durationsToUpdate.reduce((updatedGoals, goalNameToUpdate) => {
			// get the goal from the updated goals list that matches the user's
			// desired prioritized goal
			// used find without any extra optional parameters
			// give it a simple function which takes in a Goal parameter (object) and see's if the goal's
			// name is equal to the nextPrioritizedGoal. If so, return that object!
			const goalIndex =  updatedGoals.findIndex(Goal => Goal["goal"] === goalNameToUpdate);
			updatedGoals[goalIndex].duration =
				// parse the time given by the text response
				parseTime(untilSubmission(() => app.displayDialog(`Enter Custom Time Duration for ${goalNameToUpdate} (in hh:mm:ss format):`, {
						withTitle: "Update Time Durations",
						defaultAnswer: "hh:mm:ss",
						withIcon: "note",
						buttons: ["Retry", "Continue"],
						defaultButton: "Continue"
					}), displayDialogRetry).textReturned);
			return updatedGoals;
		}, todaysGoals) : // else, keep the goals as is
		todaysGoals;


	// keep prompting for the desired order until the reamainingGoals array is
	// empty
	// not the most efficient way. Too many mappings, one for each iteration....
	// loop until the reaminingGoals length == 0, then we have no more elements so we should stop
	// looping
	const todaysPrioritizedGoals =
		until(
			// the condition function to call on each iteration
			// takes in the accumulator and returns true/false
			// destructuring-bind the 3-element list of the remainingGals, the priority which we don't care about, as well as the todaysOrderedGoals which we also don't care about
			// This function receives one argument and calls length on the remaining goals
			([remainingGoals,_p,_t,_n]) => remainingGoals.length == 0, // is the length of the goals array zero?
			// the main function to call on each iteration. Takes in an acumulator and returns the next value for the accumulator
			// descturing-bind the 3-element list for remainingGoals, priority, and ordered goal list we are building
			// define the function as...
   		 	([remainingGoals, priority, todaysOrderedGoals,nextOpenTime]) => {
				// get the next prioritized goal (a string of the goal name)
				const nextPrioritizedGoalResult =
					// call the chooseFromList function like we usually do
					// redundantly map the remainingGoals list to be a list of strings each iteration
					app.chooseFromList(remainingGoals.map(Goal => Goal["goal"]), {
						// the title for the dialog
						withTitle: "It's Your Choice",
						// the prompt for the dialog
						withPrompt: `Next Goal Opening: ${nextOpenTime.toLocaleString().split(", ")[1]}\nPrioritize the next goal for your calendar:`,
						// the default selected item should be the first goal of the remainingGoals list
						defaultItems: [remainingGoals[0]["goal"]],
						// we shouldn't be allowed to select more than one element
						multipleSelectionsAllowed: false,
						// selecting no elements is not allowed
						emptySelectionAllowed: false,
						// make the okay button say this
						okButtonName: "Prioritize",
						// make the cancel button say this
						cancelButtonName: "Insert Event"
					});
				// check to see if the next prioritized goal is not false
				// if not, save and return as usual
				if (nextPrioritizedGoalResult !== false) {
					const nextPrioritizedGoal = nextPrioritizedGoalResult[0]; // get the goal out of the single-element list
					// get the goal from the remaining goals list that matches the user's desired prioritized goal
					// used find without any extra optional parameters
					// give it a simple function which takes in a Goal parameter (object) and see's if the goal's
					// name is equal to the nextPrioritizedGoal. If so, return that object!
					const Goal = remainingGoals.find(Goal => Goal["goal"] === nextPrioritizedGoal);
					// set priority and push into array
					Goal["priority"] = priority; // change the Goal's priority to the current priority accumulator
					//app.displayDialog(JSON.stringify(Goal));
					todaysOrderedGoals.push(Goal); // push the goal into the ordered goals array of objects

					// determine when the next open time would be given the nextPrioritizedGoal
					const goalEnd = simulateSavingEvent(new Date(nextOpenTime.getTime()), Goal.duration);

					// return the list for the accumulator
					// increment the priority
					// this confusing logic filters the remaining goals list for the goals that are not
					// the goal the user wants to prioritize
					return [remainingGoals.filter(Goal => Goal["goal"] !== nextPrioritizedGoal),
						// increment the priority accumulator
						priority + 1,
						// keep track of our building up of the goals array
						todaysOrderedGoals, goalEnd];
				} // end if
				// else the user wants to insert a custom event
				else {

					// two prompts. 1: What is the event name to attach priority to
					// 				2: What is the time duration of the event (hh:mm:ss)

					// prompt 1 for the goal name
					const eventName = untilSubmission(() => app.displayDialog(`Next Event Opening: ${nextOpenTime.toLocaleString().split(", ")[1]}\nEnter Event Title`, {
						withTitle: "Event - Step 1",
						defaultAnswer: "",
						withIcon: "note",
						buttons: ["Retry", "Next"],
						defaultButton: "Next"
					}), displayDialogRetry).textReturned;

					// prompt 2 for the goal's time duration (hh:mm:ss)
					// parseTime parses text of the form (hh:mm:ss) and returns an object
					// of the form {hour: hh, minute: mm, second: ss}
					// gross code to create a lambda and call it!
					const func = () => {
						const tmpResponse = untilSubmission(() => app.displayDialog(`Next Event Opening: ${nextOpenTime.toLocaleString().split(", ")[1]}\nEnter Time Duration for ${eventName} (in hh:mm:ss format):`, {
							withTitle: "Event - Step 2",
							defaultAnswer: "hh:mm:ss",
							withIcon: "note",
							buttons: ["Retry", "Submit"],
							defaultButton: "Submit"
						}), displayDialogRetry);
						// parse the time given by the text response
						const tmpTime = parseTime(tmpResponse.textReturned);
						return [tmpTime, tmpResponse];
					}
					[eventDuration, eventDurationPromptResult] = func();

					const eventEnd = simulateSavingEvent(new Date(nextOpenTime.getTime()), eventDuration);

					// add to the list the new goal
					todaysOrderedGoals.push({goal: eventName, duration: eventDuration, priority: priority});

					return [remainingGoals, priority + 1, todaysOrderedGoals, eventEnd];
				}
		// end of main function for until. For the initial value of the accumulator,
		// give todaysGoals list with the first goal as priority one and the empty list to build the ordered goals from
	}, [todaysUpdatedGoals, 1, [], app.currentDate()])[2]; // return from the until returned list the third item, the ordered goals!
	const todaysOrderedUpdatedGoals = todaysPrioritizedGoals.sort(sortOnPriority);



	// reduce on todaysOrderedGoals, returning the end time of the last event saved
	// save all the events, one after the other. they were ordered by priority as desired
	todaysOrderedUpdatedGoals.reduce((eventStart, Goal) => {
		return saveEvent(Goal.goal, eventStart, Goal.duration);
	}, app.currentDate());

	if (
	app.displayAlert("You're all set! Would you like to save your changes to the misc. goals?", {
		as: "informational",
		buttons: ["No", "Yes"],
		defaultButton: "Yes",
		cancelButton: "No",
		givingUpAfter: 10
	}).buttonReturned == "Yes") {
	// write the miscellaneous goals to the JSON
	const dateString = Date().toString();
	const desktopString = app.pathTo("desktop").toString();
	const text = `${JSON.stringify(miscGoalsToSave)}\n`;
	const file = `${desktopString}/miscJSON.txt`;
	writeTextToFile(text, file, true); // true for overwrite
	}
	//
} // end for if
else {
	if (
	app.displayAlert("You're all set! Would you like to save your changes to the misc. goals?", {
		as: "informational",
		buttons: ["No", "Yes"],
		defaultButton: "Yes",
		cancelButton: "No",
		givingUpAfter: 10
	}).buttonReturned == "Yes") {
	// write the miscellaneous goals to the JSON
	var dateString = Date().toString();
	var desktopString = app.pathTo("desktop").toString();
	var text = `${JSON.stringify(miscGoalsToSave)}\n`;
	var file = `${desktopString}/miscJSON.txt`;
	writeTextToFile(text, file, true); // true for overwrite
	}
	"Have a fun, open day!";
}
#+end_src
*** AddRecurringGoals.scpt
#+begin_src js
  var app = Application.currentApplication();
app.includeStandardAdditions = true;

// a high-order function to continue prompting the user for input while
// they "haven't submitted" the form, which will be defined in the funNotSubmitted
// lambda and called each time until it returns false, denoting the user has submitted
function untilSubmission(funGetInput, funNotSubmitted) {
	var result;
	do { result = funGetInput(); } while (funNotSubmitted(result));
	return result;
}

// generalized until construct
// allow for use of an accumulator
// said, "Until condition for accumulator is true, keep calling fun"
function until(condition, fun, accumulator) {
	// while the condition isn't true, keep iterating
	while(!condition(accumulator))
		accumulator = fun(accumulator);
	return accumulator;
}

const parseTime = timeString => { return {hour: parseInt(timeString.substring(0,2)), minute: parseInt(timeString.substring(3,5)), second: parseInt(timeString.substring(6,8))}; }

// used for miscJSON.txt
function writeTextToFile(text, file, overwriteExistingContent) {
    try {

        // Convert the file to a string
        var fileString = file.toString();

        // Open the file for writing
        var openedFile = app.openForAccess(Path(fileString), { writePermission: true });

        // Clear the file if content should be overwritten
        if (overwriteExistingContent) {
            app.setEof(openedFile, { to: 0 });
        }

        // Write the new content to the file
        app.write(text, { to: openedFile, startingAt: app.getEof(openedFile) });

        // Close the file
        app.closeAccess(openedFile);

        // Return a boolean indicating that writing was successful
        return true;
    }
    catch (error) {

        try {
            // Close the file
            app.closeAccess(file);
        }
        catch(error) {
            // Report the error is closing failed
            console.log(`Couldn't close file: ${error}`);
        }

        // Return a boolean indicating that writing was unsuccessful
        return false;
    }
}

// a fun for what is true in "not submitted" terms for
// the "chooseFromList" user interface
const chooseFromListRetry = dialogResult => dialogResult === false;
const displayDialogRetry = dialogResult => dialogResult.buttonReturned === "Retry";

const newRecurringGoals =
	// until the user is done creating misc. goals to work on, keep prompting
	until(([_m, _p, lastGoalDialogSubmission]) => lastGoalDialogSubmission.buttonReturned === "I'm Done",
		// the main function
		([recurringGoals, priority, _]) => {
			// two prompts. 1: What is the goal name to attach priority to
			// 				2: What is the time duration of the goal (hh:mm:ss)

			// prompt 1 for the goal name
			const goalName = untilSubmission(() => app.displayDialog("Enter Goal Title", {
				withTitle: "New Recurring Goal - Step 1",
				defaultAnswer: "",
				withIcon: "note",
				buttons: ["Retry", "Next"],
				defaultButton: "Next"
			}), displayDialogRetry).textReturned;

			// prompt 2 for the goal's time duration (hh:mm:ss)
			// parseTime parses text of the form (hh:mm:ss) and returns an object
			// of the form {hour: hh, minute: mm, second: ss}
			// gross code to create a lambda and call it!
			const func = () => {
				const tmpResponse = untilSubmission(() => app.displayDialog(`Enter Time Duration for ${goalName} (in hh:mm:ss format):`, {
					withTitle: "New Recurring Goal - Step 2",
					defaultAnswer: "hh:mm:ss",
					withIcon: "note",
					buttons: ["I'm Done", "Retry", "Create Another"],
					defaultButton: "Create Another"
				}), displayDialogRetry);
				// parse the time given by the text response
				const tmpTime = parseTime(tmpResponse.textReturned);
				return [tmpTime, tmpResponse];
			}
			[goalDuration, goalDurationPromptResult] = func();

			// add to the list the new goal
			recurringGoals.push({goal: goalName, duration: goalDuration, priority: priority});

			// return the updated list, an increasing priority, and the submission
			// of the goalDurationPromptResult
			return [recurringGoals, priority + 1, goalDurationPromptResult];
		}, [[], 1, {buttonReturned: ""} /* for compatability reasons */])[0];

const recurringGoals = JSON.parse(app.read(Path("/Users/benjaminhansen/Desktop/recurringGoalsJSON.txt")));

// combine the two
const newRecurringGoalsToSave = recurringGoals.concat(newRecurringGoals);

// write the miscellaneous goals to the JSON
const dateString = Date().toString();
const desktopString = app.pathTo("desktop").toString();
const text = `${JSON.stringify(newRecurringGoalsToSave)}\n`;
const file = `${desktopString}/recurringGoalsJSON.txt`;
writeTextToFile(text, file, true); // true for overwrite
#+end_src
*** AddMiscGoals.scpt
#+begin_src js
  var app = Application.currentApplication();
app.includeStandardAdditions = true;

// a high-order function to continue prompting the user for input while
// they "haven't submitted" the form, which will be defined in the funNotSubmitted
// lambda and called each time until it returns false, denoting the user has submitted
function untilSubmission(funGetInput, funNotSubmitted) {
	var result;
	do { result = funGetInput(); } while (funNotSubmitted(result));
	return result;
}

// generalized until construct
// allow for use of an accumulator
// said, "Until condition for accumulator is true, keep calling fun"
function until(condition, fun, accumulator) {
	// while the condition isn't true, keep iterating
	while(!condition(accumulator))
		accumulator = fun(accumulator);
	return accumulator;
}

const parseTime = timeString => { return {hour: parseInt(timeString.substring(0,2)), minute: parseInt(timeString.substring(3,5)), second: parseInt(timeString.substring(6,8))}; }

// used for miscJSON.txt
function writeTextToFile(text, file, overwriteExistingContent) {
    try {

        // Convert the file to a string
        var fileString = file.toString();

        // Open the file for writing
        var openedFile = app.openForAccess(Path(fileString), { writePermission: true });

        // Clear the file if content should be overwritten
        if (overwriteExistingContent) {
            app.setEof(openedFile, { to: 0 });
        }

        // Write the new content to the file
        app.write(text, { to: openedFile, startingAt: app.getEof(openedFile) });

        // Close the file
        app.closeAccess(openedFile);

        // Return a boolean indicating that writing was successful
        return true;
    }
    catch (error) {

        try {
            // Close the file
            app.closeAccess(file);
        }
        catch(error) {
            // Report the error is closing failed
            console.log(`Couldn't close file: ${error}`);
        }

        // Return a boolean indicating that writing was unsuccessful
        return false;
    }
}

// a fun for what is true in "not submitted" terms for
// the "chooseFromList" user interface
const chooseFromListRetry = dialogResult => dialogResult === false;
const displayDialogRetry = dialogResult => dialogResult.buttonReturned === "Retry";

// may be empty. if the user hasMiscGoalsToAdd, then call the until function, else return the empty list
const newMiscGoals =
	// until the user is done creating misc. goals to work on, keep prompting
	until(([_m, _p, lastGoalDialogSubmission]) => lastGoalDialogSubmission.buttonReturned === "I'm Done",
		// the main function
		([miscGoals, priority, _]) => {
			// two prompts. 1: What is the goal name to attach priority to
			// 				2: What is the time duration of the goal (hh:mm:ss)

			// prompt 1 for the goal name
			const goalName = untilSubmission(() => app.displayDialog("Enter Goal Title", {
				withTitle: "New Misc. Goal",
				defaultAnswer: "",
				withIcon: "note",
				buttons: ["Retry", "Next"],
				defaultButton: "Next"
			}), displayDialogRetry).textReturned;

			// prompt 2 for the goal's time duration (hh:mm:ss)
			// parseTime parses text of the form (hh:mm:ss) and returns an object
			// of the form {hour: hh, minute: mm, second: ss}
			// gross code to create a lambda and call it!
			const func = () => {
				const tmpResponse = untilSubmission(() => app.displayDialog(`Enter Time Duration for ${goalName} (in hh:mm:ss format):`, {
					withTitle: "New Misc. Goal",
					defaultAnswer: "hh:mm:ss",
					withIcon: "note",
					buttons: ["I'm Done", "Retry", "Create Another"],
					defaultButton: "Create Another"
				}), displayDialogRetry);
				// parse the time given by the text response
				const tmpTime = parseTime(tmpResponse.textReturned);
				return [tmpTime, tmpResponse];
			}
			[goalDuration, goalDurationPromptResult] = func();

			// add to the list the new goal
			miscGoals.push({goal: goalName, duration: goalDuration, priority: priority});

			// return the updated list, an increasing priority, and the submission
			// of the goalDurationPromptResult
			return [miscGoals, priority + 1, goalDurationPromptResult];
		}, [[], 1, {buttonReturned: ""} /* for compatability reasons */])[0];

const misc = JSON.parse(app.read(Path("/Users/benjaminhansen/Desktop/miscJSON.txt")));

// combine the two
const miscGoalsToSave = misc.concat(newMiscGoals);

// write the miscellaneous goals to the JSON
const dateString = Date().toString();
const desktopString = app.pathTo("desktop").toString();
const text = `${JSON.stringify(miscGoalsToSave)}\n`;
const file = `${desktopString}/miscJSON.txt`;
writeTextToFile(text, file, true); // true for overwrite
#+end_src
*** ShiftSchedule.scpt
  If I got off of schedule, I can re-shift my plans with this script.
#+begin_src js
  var app = Application.currentApplication();
app.includeStandardAdditions = true;

// with help from https://developer.apple.com/library/archive/documentation/AppleApplications/Conceptual/CalendarScriptingGuide/Calendar-LocateanEvent.html#//apple_ref/doc/uid/TP40016646-CH95-SW7
var Calendar = Application("Calendar")

// a high-order function to continue prompting the user for input while
// they "haven't submitted" the form, which will be defined in the funNotSubmitted
// lambda and called each time until it returns false, denoting the user has submitted
function untilSubmission(funGetInput, funNotSubmitted) {
	var result;
	do { result = funGetInput(); } while (funNotSubmitted(result));
	return result;
}

// generalized until construct
// allow for use of an accumulator
// said, "Until condition for accumulator is true, keep calling fun"
function until(condition, fun, accumulator) {
	// while the condition isn't true, keep iterating
	while(!condition(accumulator))
		accumulator = fun(accumulator);
	return accumulator;
}

const parseTime = timeString => { return {hour: parseInt(timeString.substring(0,2)), minute: parseInt(timeString.substring(3,5)), second: parseInt(timeString.substring(6,8))}; }

// used for miscJSON.txt
function writeTextToFile(text, file, overwriteExistingContent) {
    try {

        // Convert the file to a string
        var fileString = file.toString();

        // Open the file for writing
        var openedFile = app.openForAccess(Path(fileString), { writePermission: true });

        // Clear the file if content should be overwritten
        if (overwriteExistingContent) {
            app.setEof(openedFile, { to: 0 });
        }

        // Write the new content to the file
        app.write(text, { to: openedFile, startingAt: app.getEof(openedFile) });

        // Close the file
        app.closeAccess(openedFile);

        // Return a boolean indicating that writing was successful
        return true;
    }
    catch (error) {

        try {
            // Close the file
            app.closeAccess(file);
        }
        catch(error) {
            // Report the error is closing failed
            console.log(`Couldn't close file: ${error}`);
        }

        // Return a boolean indicating that writing was unsuccessful
        return false;
    }
}

// saves an event to my calendar
function saveEvent(eventTitle, eventStart, duration) {
	var eventEnd = new Date(eventStart.getTime()); // make it an end date!
	// time overflows will be taken care of in the set functions
	eventEnd.setHours(eventStart.getHours() + duration.hour); // in military time
	eventEnd.setMinutes(eventStart.getMinutes() + duration.minute);
	eventEnd.setSeconds(eventStart.getSeconds() + duration.second);

	const projectCalendars = Calendar.calendars.whose({name: "benjamminhansen@gmail.com"});
	const projectCalendar = projectCalendars[0];
	const event = Calendar.Event({summary: eventTitle, startDate: eventStart, endDate: eventEnd, description: Object.values(duration).reduce((str, time) => {
		var strTime = time.toString(); // may only be one char
		// see if we need to pad the number with a zero
		if (strTime.length == 1) {
			strTime = "0" + strTime;
		}
		str += strTime + ":";
		return str;
	}, "").slice(0,-1)/* slice off last hanging ':' */});
	projectCalendar.events.push(event);
	return eventEnd; // return the end date of this event we saved
}

// a fun for what is true in "not submitted" terms for
// the "chooseFromList" user interface
const chooseFromListRetry = dialogResult => dialogResult === false;
const displayDialogRetry = dialogResult => dialogResult.buttonReturned === "Retry";



var startDate = app.currentDate()
startDate = startDate
startDate.setHours(0)
startDate.setMinutes(0)
startDate.setSeconds(0)
var endDate = app.currentDate()
endDate.setHours(23)
endDate.setMinutes(59)
endDate.setSeconds(59)

var projectCalendars = Calendar.calendars.whose({name: "benjamminhansen@gmail.com"})
var projectCalendar = projectCalendars[0]
var events = projectCalendar.events.whose({startDate: {_greaterThan: startDate}, endDate: {_lessThanEquals: endDate}})
/*
var event1 = events[0]
var event2 = events[1]
var event3 = events[2]
app.displayDialog([event1.description(), event2.description(), event3.description()].toString());*/
// Step 1: Convert the list to a list of summaries, which will be used to access the events
const goalsToFinish = untilSubmission(() => app.chooseFromList(Array.from(Array(events.length).keys()).map(index => events[index].summary()), {
		withPrompt: "Which remaining goals do you want to accomplish from now on?",
		withTitle: "Choose Events to Shift",
		emptySelectionAllowed: false,
		multipleSelectionsAllowed: true,
		okButtonName: "Shift",
		cancelButtonName: "Retry"
}), chooseFromListRetry);

const goalsToShift = goalsToFinish.reduce((goals, goalName)=> {
	//app.displayDialog(events.whose({summary: goalName})[0].description());
	goals.push({goal: goalName, duration: parseTime(events.whose({summary: goalName})[0].description())});
	return goals;
}, []);

// run our Applescript Script to delete events where their
// end date is within now to midnight. Avoid duplicates!
app.runScript(Path("/../Downloads/DeleteFromAnHourAgoUntilMidnight.scpt"));


goalsToShift.reduce((eventStart, Goal) => {
		return saveEvent(Goal.goal, eventStart, Goal.duration);
	}, app.currentDate());
#+end_src
*** Goal Scheduler.scpt
  A menu-based interface for the user to choose which application to run
#+begin_src js
  // with help from the second post in https://stackoverflow.com/questions/28773207/how-to-get-posix-path-of-the-current-scripts-folder-in-javascript-for-automatio
ObjC.import("Cocoa");
var app = Application.currentApplication()
app.includeStandardAdditions = true
const thePath = app.pathTo(this);

// use ObjC components
var thePathStr = $.NSString.alloc.init;
thePathStr = $.NSString.alloc.initWithUTF8String(thePath);
const thePathStrDir = (thePathStr.stringByDeletingLastPathComponent);

// coerce to a JS string with .js
const fullPathToApp = thePathStrDir.js + "/";

const chooseFromListCancel = dialogResult => dialogResult === false;

// generalized until construct
// allow for use of an accumulator
// said, "Until condition for accumulator is true, keep calling fun"
function until(condition, fun, accumulator) {
	// while the condition isn't true, keep iterating
	while(!condition(accumulator))
		accumulator = fun(accumulator);
	return accumulator;
}

const dynamicSchedule = () => app.runScript(fullPathToApp + "DynamicSchedule.scpt");
const shiftSchedule = () => app.runScript(fullPathToApp + "DynamicSchedule.scpt");
const addMiscGoals = () => app.runScript(fullPathToApp + "AddMiscGoals.scpt");
const addRecurringGoals = () => app.runScript(fullPathToApp + "AddRecurringGoals.scpt");
const calConsumer = () => app.runScript(fullPathToApp + "CalConsumer.scpt");

const scriptOptions = {
	"Generate Today's Dynamic Schedule": dynamicSchedule,
	"Shift Schedule": shiftSchedule,
	"Add Misc. Goals": addMiscGoals,
	"Add Recurring Goals": addRecurringGoals,
	"Calorie Consumer": calConsumer
};

const promptForAppToRun = () =>
	app.chooseFromList(Object.keys(scriptOptions), {
		withTitle: "Goal Scheduler Menu",
		withPrompt: "Applications:",
		defaultItems: ["Generate Today's Dynamic Schedule"],
		emptySelectionAllowed: false,
		multipleSelectionsAllowed: false,
		okButtonName: "Run",
		cancelButtonName: "Quit"
	});
const runAppIfDesired = (_) => {
	const dialogResult = app.chooseFromList(Object.keys(scriptOptions), {
			withTitle: "Goal Scheduler Menu",
			withPrompt: "Applications:",
			defaultItems: ["Generate Today's Dynamic Schedule"],
			emptySelectionAllowed: false,
			multipleSelectionsAllowed: false,
			okButtonName: "Run",
			cancelButtonName: "Quit"
		});

	if (dialogResult) { // is true
		// there is only one list item. Run it's corresponding application script
		scriptOptions[dialogResult[0]](); // run the function
	}
	return dialogResult;
}


// keep looping until the user is done (quits the menu app)
// go until the user hits "Quit"
until(chooseFromListCancel,
	runAppIfDesired,
	// initial accumulator result
	runAppIfDesired());
#+end_src
** TODO Hangman.app                                                :noexport:
* TODO Class Projects                                              :noexport:
** TODO Project Manager for Team of 3, Bit-by-Bit                  :noexport:
** TODO Discrete Mathematics                                       :noexport:
** TODO Mathematica for a Physics Project                          :noexport:
** TODO Machine Learning Data Manipulation, Emotion                :noexport:
** TODO Back Pain Reading Times                                    :noexport:
*** TODO Design the Study                                          :noexport:
*** TODO Collect the Data                                          :noexport:
*** TODO Describe the Data                                         :noexport:
*** TODO Make Inference                                            :noexport:
*** TODO Take Action                                               :noexport:
** TODO Team Lead for Team of 5 in Class-wide Project              :noexport:
* Research
** Research & Creative Works Conference
  As another side project (February 2020), I wanted to apply the frequent pondering on factoring I had done over the past year, which had increased the past 4 months because of my discrete mathematics teaching to students as a tutor and TA. I researched a proof for the conjecture I had that to find the next integer factor of a number and test if it's factor, one could simply subtract from the last factor checked the division of that previous factor and the current integer.

  The research is presented in the form of a website on my GitHub.io here: https://benjaminrhansen.github.io/Theorem/.

  Here is the theorem.
*** Theorem
    Let \(n \in \mathbb{n}\) For \(k\) = 1, \dots, \(\lfloor\sqrt{n}\rfloor\), let \(a_k\) be the real number
  such that \(a_{k}k = n\). The numbers \(a_1, a_2, \dots, a_{\lfloor\sqrt{n}\rfloor}\) satisfy the recurrence relation

  \(a_k = a_{k-1} \)\minus\( \frac{a_{k-1}}{k}\)

** TODO Fractional Library                                         :noexport:
** Multithreading: How many threads? - White Paper
   In my Operating Systems class, our final project was a research paper on how many threads are needed for a given program to maximize performance.

   Here is the file for the white paper:
   file:./WhitePaper.pdf
* TODO Graduation Plans                                            :noexport:
*** TODO July 2021                                                 :noexport:
*** TODO Internship                                                :noexport:
*** TODO Senior Project                                            :noexport:
* Blog
** <2020-07-24 Fri>
*** Debugging
Today I was doing quick coding in JavaScript, working on my Summer side project you can view here: [[https://benjaminrhansen.github.io/Portfolio/#outline-container-org4ad206c][Automate Summer Workflow]].
It was all going fairly well until I created a whole block of code I understood logically would work (or should, because it made
since to me) but it didn't. It would work on the first loop, and then not on any of the others. I was so confused. I displayed some
outputs, pondered what might be affecting the error given the message I was receiving. I made some minor changes, and still - the same bug prevailed.

So, I had to get in to the nitty-gritty and *comment every single line*. After commenting thoroughly each little
piece of the block, I got down to almost the last little bit without any idea what might have been wrong and found the bug.
I thought, what is this doing here? How did it still run once and then quit. It was very weird. After deleting that extra unneeded stuff, it worked.
*** Code Snippet before
#+begin_src js
  // keep prompting for the desired order until the reamainingGoals array is
  // empty
  const todaysOrderedGoals =
        until(
            ([remainingGoals,_p,_t]) => remainingGoals.length == 0,
                ([remainingGoals, priority, todaysOrderedGoals]) => {
                const nextPrioritizedGoal =
                      untilSubmission(function() {
                          return app.chooseFromList(remainingGoals.map(Goal => Goal["goal"]), {
                              withTitle: "It's Your Choice",
                              withPrompt: "Prioritize the next goal for your calendar:",
                              defaultItems: [remainingGoals[0]["goal"]],
                              multipleSelectionsAllowed: false,
                              emptySelectionAllowed: false,
                              okButtonName: "Prioritize",
                              cancelButtonName: "Retry"
                          });
                      },chooseFromListRetry)[0];
                const Goal = remainingGoals.find(Goal => Goal["goal"] === nextPrioritizedGoal);
                Goal["priority"] = priority; // change the Goal's priority to the current priority accumulator
                app.displayDialog(JSON.stringify(Goal));
                todaysOrderedGoals.push(Goal);
                app.displayDialog(JSON.stringify(todaysOrderedGoals));
                return [remainingGoals.filter(Goal => Goal["goal"] !== nextPrioritizedGoal),
                        priority + 1,
                        todaysOrderedGoals];
            }, [todaysGoals, 1, []])[2];
#+end_src
*** Code Snippet after
#+begin_src js
  // keep prompting for the desired order until the reamainingGoals array is
  // empty
  // not the most efficient way. Too many mappings, one for each iteration....
  // loop until the reaminingGoals length == 0, then we have no more elements so we should stop
  // looping
  const todaysOrderedGoals =
        until(
            // the condition function to call on each iteration
            // takes in the accumulator and returns true/false
            // destructuring-bind the 3-element list of the remainingGals, the priority which we don't care about, as well as the todaysOrderedGoals which we also don't care about
            // This function receives one argument and calls length on the remaining goals
            ([remainingGoals,_p,_t]) => remainingGoals.length == 0, // is the length of the goals array zero?
            // the main function to call on each iteration. Takes in an acumulator and returns the next value for the accumulator
            // descturing-bind the 3-element list for remainingGoals, priority, and ordered goal list we are building
            // define the function as...
                ([remainingGoals, priority, todaysOrderedGoals]) => {
                // for debugging purposes
                //app.displayDialog("Going in: ".concat(JSON.stringify(remainingGoals)));
                // get the next prioritized goal (a string of the goal name)
                const nextPrioritizedGoal =
                      // until submission will return a list of goals
                      // since only one item will be in the list, like we want, choose the first
                      // goal
                      // loop until the submission is complete (the user didn't hit "retry"
                      untilSubmission(function() {
                          // we must return or else undefined will be returned for this function
                          // call the chooseFromList function like we usually do
                          // redundantly map the remainingGoals list to be a list of strings each iteration
                          return app.chooseFromList(remainingGoals.map(Goal => Goal["goal"]), {
                              // the title for the dialog
                              withTitle: "It's Your Choice",
                              // the prompt for the dialog
                              withPrompt: "Prioritize the next goal for your calendar:",
                              // the default selected item should be the first goal of the remainingGoals list
                              defaultItems: [remainingGoals[0]["goal"]],
                              // we shouldn't be allowed to select more than one element
                              multipleSelectionsAllowed: false,
                              // selecting no elements is not allowed
                              emptySelectionAllowed: false,
                              // make the okay button say this
                              okButtonName: "Prioritize",
                              // make the cancel button say this
                              cancelButtonName: "Retry"
                          });
                          // give the untilSubmission function the chooseFromListRetry function which
                          // returns true if the result was false (meaning the user "cancelled" the dialog)
                      },chooseFromListRetry)[0]; // get the goal out of the single-element list
                // get the goal from the remaining goals list that matches the user's desired prioritized goal
                // used find without any extra optional parameters
                // give it a simple function which takes in a Goal parameter (object) and see's if the goal's
                // name is equal to the nextPrioritizedGoal. If so, return that object!
                const Goal = remainingGoals.find(Goal => Goal["goal"] === nextPrioritizedGoal);
                // set priority and push into array
                Goal["priority"] = priority; // change the Goal's priority to the current priority accumulator
                app.displayDialog(JSON.stringify(Goal));
                todaysOrderedGoals.push(Goal); // push the goal into the ordered goals array of objects
                app.displayDialog(JSON.stringify(todaysOrderedGoals));
                // return the list for the accumulator
                // increment the priority
                // this confusing logic filters the remaining goals list for the goals that are not
                // the goal the user wants to prioritize
                return [remainingGoals.filter(Goal => Goal["goal"] !== nextPrioritizedGoal),
                        // increment the priority accumulator
                        priority + 1,
                        // keep track of our building up of the goals array
                        todaysOrderedGoals];
                // end of main function for until. For the initial value of the accumulator,
                // give todaysGoals list with the first goal as priority one and the empty list to build the ordered goals from
            }, [todaysGoals, 1, []])[2]; // return from the until returned list the third item, the ordered goals!
#+end_src
** <2020-08-18 Wed>
   The RSA cryptosystem works so beautifully but I wanted to see the mathematical theory behind the guarantee that the encryption and then decryption of any encoded message will result in the original message. Here is a proof I made to verify that the message is preserved when encrypted and then decrypted.

  Recall the "players" (variables) of RSA encryption. This table was created by Rick Neff in [[https://rickneff.github.io/metaphors-be-with-you.html#outline-container-org844de28][Metaphor's Be With You]] under section "Two".


| Who | What                    | Roles/Relationships                                                 |
|-----+-------------------------+---------------------------------------------------------------------|
| m   | message (plaintext)     | decrypted c: m = c^d % n                                             |
| c   | ciphertext              | encrypted m: c = m^e % n                                             |
| p   | a large prime           | where “large” is relative                                           |
| q   | another large prime     | different than p, but with the same number of digits (more or less) |
| n   | the modulus             | the product of p and q: must be bigger than m                       |
| t   | the totient             | the product of p - 1 and q - 1                                      |
| e   | the encryption exponent | must be coprime to t, typically 65537 (216 + 1)                     |
| d   | the decryption exponent | TUMMI (mod t) of e                                                  |
From Metaphor's Be With You - Rick Neff.

  I want to prove that \(m\) really does equal \(c^d \text{ mod } n\).

  Since \(c = m^e \text{ mod } n\)
\begin{aligned}
  c^d \text{ mod } n &= (m^e \text{ mod } n)^d \text{ mod } n \\
     &=(m^e \text{ mod } n) \times (m^e \text{ mod } n) \times \cdots (d \text{ times}) \\
     &=(m^e \text{ mod } n \times \cdots (d \text{ times})) \times (m^e \text{ mod } n \times \cdots (d \text{ times})) \text{ by the distributivity of modular arithmetic} \\
     &=m^{e+e+\cdots(d \text{ times})}\text{ mod } n \text{ by the property of reflexivity} \\
     &=m^{e\cdot d}\text{ mod } n \\
     &=m^{e\cdot d \text{ mod }n}\text{ mod }n \text{ by the fact that } \forall a, k, b \in \mathbb{N} : a^k \text{ mod} b \equiv a^{k \text{ mod} b}\text{ mod}b \\
     &=m
\end{aligned}
This last simplification needs some explanation, which I will explain hopefully soon...
* Bio
** My Beginnings
  Hi! I'm Benjamin Hansen. I am from Southern Utah. I became interested in computer science in high school where I took 5 computer courses. I went on a mission for my church, [[https://churchofjesuschrist.org][The Church of Jesus Christ of Latter-day Saints]], in Georgia, worked construction for 4 months, and then started my Bachelor's of Science in Computer Science at [[https://www.byui.edu/][Brigham Young University - Idaho]]. I chose Computer Science because of how it peaked my interest in my web design & development and programming course in high school. What really brought me confidence that I could do well in this field was from achieving the highest grade on the final exam of my entire programming class.

  Ever since, programming paradigms and concepts have come easily to me. I can quickly learn languages and write programs in them. What I really love is Erlang. I feel like Erlang is almost as easy as Python. My initial solution to a problem is not hard to implement in Erlang, whereas in other languages it may take many revisions to my initial solution to solve the problem. I've created many modules with many functions in Erlang, for a class and on my free time. I prefer doing mathematical algorithms and functions in Erlang. I love its scalability and simplicity.

  I've also used my C++ knowledge in 5 semesters of courses. After learning Python during my Machine Learning course, I learned R within a short span of time to do the final project. I feel confident I can learn any software and accomplish any task given to me.
