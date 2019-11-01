# CSharpBackEndLiveProject

The last two weeks I have been contributing to a group project at The Tech Academy where we took on tasks to develop a full-scale MVC application in C#. This sprint I was focused on the back end. The software will be used to manage a collection of jobs for a client.

## Back End Stories

### Delete Job with Null values
Our Job class only has one required field-- the Job title. We are making the Create Job form more user friendly to allow for null values, and already have seed data that has null values. However, if you try to delete a job that's missing values, the system throws an error. Refactor the delete function so it handles null values, but maintains the functionality of deleting the Shift Time and Job Other associated with it when it's deleted.

Code snippet:
```
// POST: Jobs/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int? id)  // triggered when Delete "Job Title" is pressed
        {
            Job job = db.Jobs.Find(id);  // finds Job to delete in Jobs table
            ShiftTime shift = db.ShiftTime.Find(id);  //  finds shifttimes attached to Job being deleted in ShiftTimes table
            JobOther other = db.JobOthers.Find(id); //  finds JobsOthers attached to Job being deleted in JobOthers table
            var schedules = db.Schedules.Where(x => x.Job.JobIb == id);    // finds list of schedules attached to Job being deleted in Schedules table
            if (schedules != null)
            {
                foreach (Schedule schedule in schedules) ;   //  iterates through list of schedules attached to Job being deleted....
            }
            {
                foreach (Schedule schedule in schedules)
                db.Schedules.Remove(schedule);
                db.SaveChanges(); // ...and removes schedules
            }
            if (shift != null) db.ShiftTime.Remove(shift);  // removes ShiftTimes
            if (other !=null) db.JobOthers.Remove(other); // removes JobOthers
            db.Jobs.Remove(job);  // removes actual Job
            db.SaveChanges();   // finally saves changes to each table

            return RedirectToAction("Index"); // returns to "Index" list of Jobs 

        }
        ```
        
### ChatMessages/Create bug fix
If you go to ChatMessages/Create view page and try to create a message with "Empty" inputs, you are still able to create a dummy blank message with no content in it. This shouldn't happen and all input fields should be required to be filled. 

Make sure to add an error message to tell users that all fields need to be filled if they hit the create button with blank input. 
```
[HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ChatMessageId,Sender,Date,Message")] ChatMessage chatMessage)
        {
            if (ModelState.IsValid && chatMessage.Sender != null && chatMessage.Date != null && chatMessage.Message != null)
            { 
                chatMessage.ChatMessageId = Guid.NewGuid();
                db.ChatMessages.Add(chatMessage);
                db.SaveChanges();
                
            }
            else
            {
                ViewBag.ShowMessage = true;
                return View();
            }

            return RedirectToAction("Index");

        }



@if (ViewBag.ShowMessage == true)//If user does not fill in all fields to create a chat message, alert pops up.
{
    <script type="text/javascript">
        alert("Please fill in all the fields.");
    </script>
}
```

### JobSite/Create bug fix
We are experiencing some bugs in Jobsite/creation page which is preventing us from creating a Jobsite. It throws an API service-related error at the zipcode input filed.  Examine the code and fix the bug. 

Error message: There was an error within the API Service: Bad Status Code Unauthorized 

Resolution: needed new API code, figured this out by reading api documentation!

### Back end: Script Debugging
When we run the master branch, and load the developer tools, there are 5 uncaught syntax errors for various scripts. This may be impacting the ability to see the mobile view properly in our emulators. These may be due to how certain scripts are rendered, or due to the order in which they are rendered. JavaScript can be very picky about order since it is a scripting language. Track down the root cause of these errors and fix them.
