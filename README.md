# Live Project -- Internship with Prosper IT Consulting

## Introduction
During the Live Project portion of my time with Prosper IT Consulting I got to work with a group of peers on a team developing a full scale MVC Web Application in C#. This was a great opportunity to implement all I'd learned in HTML, CSS, JavaScript and C#. The code base was very large and daunting at first, but I learned how to catch my bearings quickly by focusing on the sections of code I'd been given as Stories to create.

I worked on one **Front End Story** [front end stories](#front-end-stories) creating Home Page Responsiveness and one **Back End Story** [back end stories](#back-end-stories) utilizing MVC Validation Errors and new functions to auto-calculate the current season for the website and display error messages if the user inputted the wrong season.

Since the project was so wide and vast everyone in my cohort got a chance to work on a wide array of stories. One of my favorite parts was daily stand ups and learning what the team was doing and hearing how they'd implemented new skills. It was great to be able to coordinate our efforts and knowledge!

Throughout the two weeks I also had the opportunity to work on some project management and team programming communication and [skills](#other-skills-learned) by creating an outlet for all of us in the cohort to stay connected as we move on into our development careers. I am confident that these skills will enhance my participation and coordination in future projects.

Below are descriptions of the stories I worked on, with code snippets and navigation links.

## Front End Stories
* [Home Page Responsiveness](#home-page-responsiveness)

### Home Page Responsiveness
This story addressed resizing of the Home Page Title and Description from full monitor view into tablet and mobile sizes. I worked with HTML and CSS to create the features. I learned about Media Queries and their application when wanting to resize a screen. My first attempt worked, but was not smooth enough to be accepted. I reached out to my instructors and was guided toward looking into Bootstrap. For my second attempt at the code I utilized Bootstrap columns to resize the Home Page Title and Description for mobile screens. I learned that the combination of my first attempt and my second attempt created the solution! Then all I needed to do to get the full story accepted was create a transition point through media queries, new classes for .slidetitle1 and .slideText1 to be hidden at full size screen, and show once we moved into mobile screen sizes. Here's how I did it.

### HTML with Bootstrap

          <div class="slideTitle">Test Title</div>
          <img class="image-carousel" src="@Url.Content("~/Content/Images/img1.jpg")" />
          <div class="slideText center text-outline">Test Description</div>
          <div class="slideTitle1"></div>
          <div class="slideText1 center text-outline"></div>
          <div class="slideTitle-container">
            <div class="row">
              <div class="col-lg-1 bg-black center">
                <h1>Test Title</h1>
              </div>
              <div class="col-lg-1 bg-black center">
                <h3>Test Description</h3>

              </div>
            </div>
          </div>

          <div class="dot-container">
            <span class="dot" onclick="currentSlide(1)"></span>
          </div>
        }
      }

### CSS with Media Queries

    /*Add Media Queries for responsiveness*/
    
    @media (min-width: 901px) {
    .slideTitle {
        color: var(--light-color);
        font-size: 72px;
        padding: 20px;
        position: absolute;
        top: 60px;
        left: 240px;
    }
    .slideText {
        color: var(--light-color);
        font-size: 30px;
        padding: 40px;
        position: absolute;
        bottom: 140px;
        width: 100%;
        font-weight: bold;
    }

    .slideTitle1 {
        visibility: hidden;
    }

    .slideText1 {
        visibility: hidden;
    }

    .slideTitle-container {
        visibility: hidden;
    }
    }

    @media (max-width: 900px) {
    /*Styles for screen with max width of 901px*/
    .slideTitle {
        visibility: hidden;
    }

    .slideText {
        visibility: hidden;
    }

    .slideTitle1 {
        font-size: 30px;
        font-weight: bold;
        text-align: center;
    }

    .slideText1 {
        font-size: 20px;
        font-weight: bold;
        text-align: center;
    }

    .slideTitle-container {
    }
    }
    /*End Media Queries for Responsiveness*/

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#live-project)*




## Back End Stories
* [Auto Calculate Current Season](#auto-calculate-current-season)



### Auto Calculate Current Season
This story included creating a Model State Error and utilizing MVC Validation Methods. I needed to create a function to auto calculate the Theater's current season and every season thereafter by updating the SettingsUpdate() method. Then utilize this code to see if the user/admin input correlated to the current season. If it didn't I needed to create an error message to show.

This project was very challenging for me! Almost too challenging. I felt like I was in over my head. It took a full day of research into MVC Validation Methods and Model State Error's to figure out a way to get the error message to show. I stayed resilient amidst the challenges and kept pressing forward.

### MVC Validation Method's and Model State Error's
In Dashboard.cshtml:

       <!--ADD for Alert Message-->
       @Html.ValidationSummary(true, "", new { @class = "text-danger" })
       <!--END-->

      @using (Html.BeginForm("SettingsUpdate", "Admin", FormMethod.Post))
      {
      <div id="admin-formspace">
      CURRENT SEASON:
      <br />
      <input type="number" name="Current_Season" value="@Html.DisplayFor(model => model.current_season)">
      <!--Add Alert Message for Season Error-->
      @Html.ValidationMessageFor(model => model.current_season, "", new { @class = "text-danger" })
      <span class="error" id="Current_Season_error"></span>
      @{ 
          foreach (ModelState modelState in ViewData.ModelState.Values)
          {
            foreach (ModelError error in modelState.Errors)
            {
              <p>error</p>;
            }
          }
      }
      <!--End Add Alert-->      
 
 ### Calculating Current Season
Next I needed to create a function that actually calculated the current season and would continue calculating.

        //AUTO CALCULATION OF SEASON CODE
        public int AutoCalculateCurrentSeason()
        {
            DateTime season = new DateTime(1996, 6, 1);
            DateTime now = DateTime.Now;
            int currentSeason = now.Year - season.Year;

            if (now.Month < season.Month || (now.Month == season.Month && now.Day < season.Day))
                currentSeason--;

            return currentSeason;
        }
        //END AUTO CALCULATION OF SEASON CODE

 ### Amending SettingsUpdate()
Then I updated the If Statement in the SettingsUpdate() method with this new function to check and see if the user inputed the correct season and whether to display the error message or not.

        [HttpPost]
        public ActionResult SettingsUpdate(AdminSettings currentSettings)
        {
            List<SelectListItem> productionList = GetSelectListItems();
            ViewData["ProductionList"] = productionList;
            string newSettings = JsonConvert.SerializeObject(currentSettings, Formatting.Indented);
            newSettings = newSettings.Replace("T00:00:00", "");
            string filepath = Server.MapPath(Url.Content("~/AdminSettings.json"));
            string oldSettings = null;
             
            using (StreamReader reader = new StreamReader(filepath))
            {
               oldSettings = reader.ReadToEnd();
            }
            dynamic oldJSON = JObject.Parse(oldSettings);
            dynamic newJSON = JObject.Parse(newSettings);
            if (oldJSON.recent_definition.date != newJSON.recent_definition.date)
            {
                UpdateSubscribers(newJSON);
            }
            int thisSeason = AutoCalculateCurrentSeason();
            //ADDING AUTO CALC ERROR CODE HERE
            if (newJSON.current_season != thisSeason)
                //(oldJSON.current_season == 23)
                //(oldJSON.current_season != newJSON.current_season)
            {
                ModelState.AddModelError("current_season", "You have entered the incorrect Season number."); 
            }
            //END AUTO CALC ERROR CODE

### New Properties
I also created a few new properties to ensure this all worked smoothly and properly. 

    namespace TheatreCMS.Models
     {

	    public class AdminSettings
	    {
      public static DateTime Now { get; }  				//Add for current season auto calculation
      [Required(ErrorMessage = "Please enter current season number")]	// for current season validation
      public int current_season { get; set; }				// the theater season number for the current season
      public seasonProductions season_productions { get; set; }		// holds 3 production ID's for current season
      public recentDefinition recent_definition { get; set; }		// holds recent span and date
      public int onstage { get; set; }					// production ID of current production

	

*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#live-project)*


## Other Skills Learned
* Daily Stand Ups with review of peer's code, accomplishments and roadblocks.
* Communicating with team about bugs, solutions, and fixes in Tuesday/Thursday afternoon meetings and Slack channel.
* Learning about other methods of solutions by observing peer's current working status.
* Created cohesiveness amidst the group by establishing connections beyond our Live Project course.
* Learned how to create Pull Requests in Azure DevOps and moving my current working stories from New, to Active, to Resolved and Closed!
* Learned how to create new working branches in Visual Studio and connecting through Team Explorer in Visual Studio.
* Learned how to work with Azure DevOps keeping my master branch updated with the current master. Syncing, Pulling, and Committing Changes once my code was complete.

    
  
*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#live-project)*
