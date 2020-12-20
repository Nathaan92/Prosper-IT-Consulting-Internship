# Live Project

## Introduction
For the last four weeks of my time at the tech academy, I worked with my peers in a team developing a full scale MVC/MVVM Web Application in C#. It was a great learning oppertunity for fixing bugs, cleaning up code, and adding requested features. 
There were some big changes that require a lot of effort and reseach but was satisfying to come to a resolution. Through this course I had the oporitunity to see how a good developer works with what qualities and skills they posses as well as importance of team work when addressing complex issues. 
I worked on four [back end stories](#back-end-stories),one [front end stories](#front-end-stories) and a [debug](#debug). All were of varying degrees of difficulty of which I had the oportunity to complete. 
Through this sprint I was able to use my project management and team programming which I'm confident I will use again and again on future projects.
  
Below are descriptions of the stories I worked on, along with code snippets and navigation links.


## Back End Stories
* [Subscription plan 1](#Subscription-plan-1)
* [Subscription Plan 2](#Subscription-Plan-2)
* [Seed Subscription Plan](#Seed-Subscription-Plan)
* [CRUD Pages & Functionality for Subscription Plans](#CRUD-Pages-&-Functionality-for-Subscription-Plans)

The back end stories that I chose to work on during the live project were centered around the creation of a Subscripiton plan for Subscribers. I chose them specfically because not only did I want to work on an ever increaseing difficulty but I also wanted to work on a new aspect 
of the web page from start to finish. 


### Subscription plan 1
This was the first story in the project. I was tasked with the creation of a subscription plan model as well as creation of a navigation property between the subscribers and the subscription plan.

### Subscription Plan 2
This story required that I used Razor to enable a heading in my html page change its text based off of what subscription plan the used had. To to this I used HTML.Display to display the subscribers subscription.
I also seeded the user create method with a plan to ensure there would be something to display.

### Seed Subscription Plan
This story was the next stage of difficulty. Here I needed to create 3 diffrent subscription plans and seed one of them into the create subscriber method. Furthermore I also needed to allow the subscriber to change what plan they had via a drop down and if the selected plan
was the same as the users current then the button would be disabeld and be visually different. This is were it became fun beacuse I not only needed to create the seeds but make changes to controllers, views, and make styling changes. Here are a few snippits:

Here I grabbed the users current plan, grabbed the the object matching what the user selected in the drop down, and made changes to the database accordingly.

	//Post. Edits the signed in Users Subscription Plan. Recieves selected option from index page.
        	public ActionResult ChangeSubscriptionPlan(string sublevel)
        	{
           	 //Finds user ID and associated subsciber attribute values
            	string id = User.Identity.GetUserId();
            	ApplicationUser user = db.Users.Find(id);
            	Subscriber subscriber = user.SubscriberPerson;
            	//Seaches subscription plan model for attribute matching selected option from index page. Assigns subscription plan object to local variable
            	var subscriptionplan = db.SubscriptionPlan.FirstOrDefault(p => p.SubscriptionLevel == sublevel);
            	//Making changes to model and save. Returns to index page
            	subscriber.SubscriptionPlan = subscriptionplan;
            	db.Entry(subscriber).State = EntityState.Modified;
            	db.SaveChanges();
            	return RedirectToAction("Index");
        	}

This is the inline script that would disable the button if the subscription chosen is the same as the users current.


            <div class="accountbox">
              <h2 class="subback2">Manage Your Subscription</h2>
              @* C# form passing selected Subscription Plan option to dashboard controller method *@
              @using (Html.BeginForm("ChangeSubscriptionPlan", "Dashboard", FormMethod.Post)) {
                <div>
                  <h2>Current Subscription Level: @Html.DisplayFor(models => models.SubscriberPerson.SubscriptionPlan.SubscriptionLevel)</h2>
                  <p><button type="button" class="subsave">Renew Subscription</button></p>
                <div>
                  <p>
                    <label for="sublevel">Subscription Level</label>
                    @*onchange calls JS function to disable submit button if the subscription plan option selected is the same as the users current plan*@
                    <select id="sublevel" name="sublevel" onchange="subsubmitdisable()">
                      <option value="Platinum">Platinum &nbsp;&nbsp;  /  &nbsp;&nbsp;   all shows &nbsp;&nbsp;    /   &nbsp;&nbsp;  $500/yr</option>
                      <option value="Gold">Gold   &nbsp;&nbsp;  /   &nbsp;&nbsp;  any ten shows &nbsp;&nbsp;    /  &nbsp;&nbsp;   $300/yr</option>
                      <option value="Silver">Silver &nbsp;&nbsp;    /    &nbsp;&nbsp; any five shows  &nbsp;&nbsp;  /  &nbsp;&nbsp;   $200/yr</option>
                    </select>
                  </p>
                  <button type="submit" class="subsave" id="subsave">Update Subscription</button>
                  <script type="text/javascript">
                    //assignment of the subscribers current subscripton plan to a golbal variable 
                    var currentsubscription = '@Model.SubscriberPerson.SubscriptionPlan.SubscriptionLevel';
                    //assignment of the selected option value within the 'sublevel' select tags to a global variable
                    var sublevel = document.getElementById("sublevel");
                    //assigment of the 'subsave' button to a global variable
                    var subsave = document.getElementById("subsave");
                    // calls the function on page load. Ensures at page load if preselected option matches subscribers plan that button will be disabled
                    subsubmitdisable();
                    //disables and changes apperance of submit button if the subscription plan option selected is the same as the users current plan
                    function subsubmitdisable() {
                      subsave.disabled = (currentsubscription == sublevel.value);
                  }
                  </script>
                </div>
              }


### CRUD Pages & Functionality for Subscription Plans
This Final story was the most challenging are required team work with another one of my peers. In this story my task was to scaffold CRUD pages and provide the admin the ability to create or edit subscriptions plans.
These plans would have values that are not present in any other objects. In otherwords they must be original. To accomplish this I had to write two different methods to check for similarity. 

The create new method was more straight forward. I used a foreach loop to check each object in the database for similarity and if a simlar value was found it would retrun a model state error to the views page informing the user.

	public void ComparePlanOptions(SubscriptionPlan subscriptionPlan)
        	{
            	//Checks each object in dB 
            	foreach (var plan in db.SubscriptionPlan)
            	{
                	//Assigns both the users input and the objects specified attribute to strings
                	string SubscriptionLevel = subscriptionPlan.SubscriptionLevel;
                	string ExistingSubscriptionLevel = plan.SubscriptionLevel;

                	//Assigns both the users input and the objects specified attribute to strings
                	decimal PricePerYear = subscriptionPlan.PricePerYear;
                	decimal ExistingPricePerYear = plan.PricePerYear;

                	//Assigns both the users input and the objects specified attribute to strings
                	int NumberOfShows = subscriptionPlan.NumberOfShows;
                	int ExistingNumberOfShows = plan.NumberOfShows;

                	//comparason of users input and specified attribute, check for equal
                	if (ExistingSubscriptionLevel.Equals(SubscriptionLevel))
                	{
                 	   //if equal then trigger model error to display on html page
                 	   ModelState.AddModelError("SubscriptionPlan", " There is already a plan with that Subscription Level named: " + SubscriptionLevel);
                	}	
                	//comparason of users input and specified attribute, check for equal
                	else if (ExistingPricePerYear.Equals(PricePerYear))
                	{
                	    //if equal then trigger model error to display on html page
                 	   ModelState.AddModelError("SubscriptionPlan", " There is already a plan that offers a price/year of: " + PricePerYear);
                	}
                	//comparason of users input and specified attribute, check for equal
                	else if (ExistingNumberOfShows.Equals(NumberOfShows))
                	{
                  	  //if equal then trigger model error to display on html page
                  	  ModelState.AddModelError("SubscriptionPlan", " There is already a Plan that offers:  " + NumberOfShows);
                	}
            	}

The edit page required a similar approach but here I had to first seperate the plan being edited from the database and then compare the edited plan to the remaing objects to check
for similarities.

	public void CompareEditedPlans(SubscriptionPlan subscription)
        	{
            		//Assigns already existing plan with similar vaules to a var to exclude foreach loop
            		var CurrentLevel = db.SubscriptionPlan.AsNoTracking().Where(p =>
                	p.SubscriptionLevel == subscription.SubscriptionLevel ||
                	p.PricePerYear == subscription.PricePerYear ||
                	p.NumberOfShows == subscription.NumberOfShows).FirstOrDefault();
            	//Check if current plan is null, if false program continues unhindered. If true enters foreach
            	if (CurrentLevel != null)
            	{	
                	//Assignment of dB excluding CurrentLevel to Var
                	var SubscriptionPlanddB = db.SubscriptionPlan.AsNoTracking().ToList();
                	SubscriptionPlanddB.RemoveAll(cl => cl.PlanId.Equals(CurrentLevel.PlanId));

                	foreach (var plan in SubscriptionPlanddB)
                	{
                    	//Assigns both the users input and the objects specified attribute to strings
                    	string SubscriptionLevel = subscription.SubscriptionLevel;
                    	string ExistingSubscriptionLevel = plan.SubscriptionLevel;

                    	//Assigns both the users input and the objects specified attribute to strings
                    	decimal PricePerYear = subscription.PricePerYear;
                    	decimal ExistingPricePerYear = plan.PricePerYear;

                    	//Assigns both the users input and the objects specified attribute to strings
                    	int NumberOfShows = subscription.NumberOfShows;
                    	int ExistingNumberOfShows = plan.NumberOfShows;

                    	//comparason of users input and specified attribute, check for equal
                    	if (ExistingSubscriptionLevel.Equals(SubscriptionLevel))
                    	{
                    	    //if equal then trigger model error to display on html page
                    	    ModelState.AddModelError("SubscriptionPlan", " There is already a plan with that Subscription Level named: " + SubscriptionLevel);
                    	}
                    	//comparason of users input and specified attribute, check for equal
                    	else if (ExistingPricePerYear.Equals(PricePerYear))
                    	{
                     	   //if equal then trigger model error to display on html page
                     	   ModelState.AddModelError("SubscriptionPlan", " There is already a plan that offers a price/year of: " + PricePerYear);
                   	 }
                	    //comparason of users input and specified attribute, check for equal
                	    else if (ExistingNumberOfShows.Equals(NumberOfShows))
                	    {
                	        //if equal then trigger model error to display on html page
                	        ModelState.AddModelError("SubscriptionPlan", " There is already a Plan that offers:  " + NumberOfShows);
                 	   }
               	 }
           	 }
      	  }

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Debug](#Debug),[Page Top](#live-project)*


## Front End Stories
* [Change Create New Link](#Change-Create-New-Link)

### Change Create New Link
This stories goal was to make changes to a button on a page and to utilize Bootstrap classes to accomplish this. It was the first time dealing with Bootstrap class and was 
definetly a learnin experience for myself.



*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Debug](#Debug), [Page Top](#live-project)*

## Debug
* [Subscriber Bug](#Subscriber-Bug)

### Subscriber Bug
The Stories goal was to track down a bug where when a user makes a change to one of the saved values in an edit page that the change is not made to the database. The issue for this 
was that in the razor code in the EditorFor code was making changes to the wrong attribute. This was a fairly straight forward fix and was fun to track down.


  
*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Debug](#Debug), [Page Top](#live-project)*
