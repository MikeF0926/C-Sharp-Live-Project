# CSharp-LiveProject
#### The Tech Academy C# Live Project
# Introduction
#### During this project, I worked with a team of other developers to create a dyanmic website using ASP .Net MVC and Entity Framework. While working on the code itself, I also gained experience utilizing the agile methodology for software development. We participated in daily stand-up meetings where we discussed the trials we were facing and what we were working on until the next meeting. We also had a code retrospective at the end of our 10 day sprint where we discussed our successes and areas in which we needed to improve. This project was intended to give us a live experienc of being part of a development team, and I feel that the experience gained during this two weeks will be invaluable in my career.
The website is still in its early stages and isn't fully available. I'll post code snippets from my work below instead.

# Style SignIn Page
#### My first story I worked on was styling the SignIn index page, when new developers added their name to the website they would see this page. I used Bootstrap and css to help me style this portion.

```
Sign In Index Page:
<div class="text-center names-container row"> 

CSS:
.names-container > p {
    color: var(--light-color);    
    text-overflow: ellipsis;    
    white-space: nowrap;
    overflow: hidden;
    font-size: large;
    font-family: sans-serif;    
    width: 200px;    
    padding: 10px; 
    margin: 10px;
    border-radius: 30px;
    background-color: var(--main-color);    
}

```
![SignIn](https://user-images.githubusercontent.com/80483521/120860696-18448300-c554-11eb-8058-0c4fb01140cb.PNG)

# Creating a Production class model.
#### The second story I worked on was creating a entity model for a production class and also create to create the CRUD pages for that model.
# Production Model

```
        [Key]
        public int ProductionId { get; set; }
        public string Title { get; set; }
        public string Description { get; set; }
        public string Playwright { get; set; }
        public int Runtime { get; set; }

        [DataType(DataType.Date)]
        [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]        
        public DateTime OpeningDay { get; set; }

        [DataType(DataType.Date)]
        [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]        
        public DateTime ClosingDay { get; set; }
        
        [DataType(DataType.Time)]
        [DisplayFormat(DataFormatString = "{0:HH:mm}", ApplyFormatInEditMode = true)]        
        public DateTime ShowTimeEve { get; set; }

        [DataType(DataType.Time)]
        [DisplayFormat(DataFormatString = "{0:HH:mm}", ApplyFormatInEditMode = true)]        
        public DateTime? ShowTimeMat  { get; set; }

        public int Season { get; set; }
        public bool IsWorldPremiere { get; set; }
        public string TicketLink { get; set; }

        public bool IsCurrentlyShowing { get; set; }
```

# Create card layout to Production
#### My third story I had to create a card layout for the production model and also styled it.

```
@model IEnumerable<TheatreCMS3.Areas.Prod.Models.Production>

@{
    ViewBag.Title = "Index";
    Layout = "~/Views/Shared/_Layout.cshtml";
}
  <div class="container">
    <p>
      <a href="@Url.Action("Create")" class="btn pro-btn">Create New Production</a>
    </p>

    @if (Model.Count() == 0)
    {
      <p>No current Productions</p>
    }
    else
    {
      <div class="row">

        @foreach (var production in Model)
        {
          <a href="@Url.Action("Details", new { id = production.ProductionId })" class="pro-title">

            <div class="col-sm-4 pro-card">
              <div class="card bg-dark h-100">
                <div onmouseover="document.getElementById(@production.ProductionId).style.display = 'block';"
                     onmouseout="document.getElementById(@production.ProductionId).style.display = 'none';">
                  <img class="card-img-top img-fluid" src="https://via.placeholder.com/500x300" alt="assortment of photos" /> <!--added placeholder webpage of images until can link-->
                  <div class="pro-img-overlay" id="@production.ProductionId">
                    <a href="@Url.Action("Edit", new { id = production.ProductionId })" class="btn btn-sm pro-btn"><i class="fas fa-edit"></i> Edit</a>
                    <a href="@Url.Action("Delete", new { id = production.ProductionId })" class="btn btn-sm pro-btn"><i class="fa fa-trash"></i> Delete</a>
                  </div>
                </div>
                <div class="card-body text-center">
                  <span class="pro-title">@production.Title</span>
                </div>
              </div>
            </div>
          </a>
        }
      </div>
    }
  </div>
```
```
css styling
.pro-btn {
    color: var(--light-color);
    background-color: var(--main-color--light);
    border-radius: 30px;
}

.pro-btn:hover {
    color: var(--light-color);
    background-color: var(--main-color);
}

.pro-img-overlay {
    position: absolute;
    top: 5px;
    left: 5px;
    display: none;
}
```
![Image of Card](https://user-images.githubusercontent.com/80483521/120859852-cf3fff00-c552-11eb-9ea1-b57be2d833e6.PNG)

# Add pagination and search bar
#### My fourth story I had to add pagination and a search bar to the Production index page.

```
Controller page:
public ViewResult Index(string currentFilter, string searchString, int? page)
        {
            if (searchString != null)
            {
                page = 1;
            }
            else
            {
                searchString = currentFilter;
            }

            ViewBag.CurrentFilter = searchString;

            var productions = from p in db.Productions
                              select p;
            if (!String.IsNullOrEmpty(searchString))
            {
                productions = productions.Where(p => p.Title.Contains(searchString));
            }

            productions = productions.OrderBy(p => p.Title);

            int pageSize = 5;
            int pageNumber = (page ?? 1);
            return View(productions.ToPagedList(pageNumber, pageSize));
        }
```
```
Index page :
<!--Added Search function-->
  @using (Html.BeginForm("Index", "Productions", FormMethod.Get))
  {
    <p class="ml-2 p-0 cms-text-secondary">
      Find Title: <br />
      @Html.TextBox("SearchString")
      <input type="submit" value="Search" />
    </p>
  }

<!--Page Navigation-->
<p class="ml-2 my-1">
Page @(Model.PageCount < Model.PageNumber ? 0 : Model.PageNumber) of @Model.PageCount
</p>
<p>
@Html.PagedListPager(Model, page => Url.Action("Index",
    new { page, sortOrder = ViewBag.CurrentSort, currentFilter = ViewBag.CurrentFilter }))
</p>
```
![Pagination&Search](https://user-images.githubusercontent.com/80483521/120859877-d535e000-c552-11eb-9506-7cdd4f0ac88d.PNG)

# Style the create and edit page
#### My final story I had to create a form for my create and edit page of the production section. They wanted it to be a card format with the submit and cancel button to be at the bottom center of the form.

```
Create Page:
<body>
  <h4 class="text-center pro-header">Create Production</h4>
  @using (Html.BeginForm())
  {
    @Html.AntiForgeryToken()
    <div class="pro-container">
      <div class="pro-inner form-horizontal">
        <div>
          <hr />
          @Html.ValidationSummary(true, "", new { @class = "text-danger" })
          <div class="form-group">
            @Html.LabelFor(model => model.Title, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.Title, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = " Enter Title" } })
              @Html.ValidationMessageFor(model => model.Title, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Enter Description" } })
              @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.Playwright, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.Playwright, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Enter Playwright" } })
              @Html.ValidationMessageFor(model => model.Playwright, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.Runtime, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.Runtime, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Enter Runtime" } })
              @Html.ValidationMessageFor(model => model.Runtime, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.OpeningDay, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.OpeningDay, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Opening Day" } })
              @Html.ValidationMessageFor(model => model.OpeningDay, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.ClosingDay, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.ClosingDay, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Closing Day" } })
              @Html.ValidationMessageFor(model => model.ClosingDay, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.ShowTimeEve, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.ShowTimeEve, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Evening Show Time" } })
              @Html.ValidationMessageFor(model => model.ShowTimeEve, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.ShowTimeMat, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.ShowTimeMat, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Matinee Show Time" } })
              @Html.ValidationMessageFor(model => model.ShowTimeMat, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.Season, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.Season, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Season" } })
              @Html.ValidationMessageFor(model => model.Season, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.IsWorldPremiere, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              <div class="checkbox">
                @Html.EditorFor(model => model.IsWorldPremiere)
                @Html.ValidationMessageFor(model => model.IsWorldPremiere, "", new { @class = "text-danger" })
              </div>
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.TicketLink, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              @Html.EditorFor(model => model.TicketLink, new { htmlAttributes = new { @class = "form-control pro-high", placeholder = "Ticket Link" } })
              @Html.ValidationMessageFor(model => model.TicketLink, "", new { @class = "text-danger" })
            </div>
          </div>

          <div class="form-group">
            @Html.LabelFor(model => model.IsCurrentlyShowing, htmlAttributes: new { @class = "control-label col-md-2 cms-text-dark" })
            <div class="col-md-10 pro-input">
              <div class="checkbox">
                @Html.EditorFor(model => model.IsCurrentlyShowing)
                @Html.ValidationMessageFor(model => model.IsCurrentlyShowing, "", new { @class = "text-danger" })
              </div>
            </div>
          </div>

          <div class="form-group">
            <div class="col-md-offset-2 col-md-10 text-center">
              <div>
                <input type="submit" value="Submit" class="pro-create btn" />
                @Html.ActionLink("Cancel", "Index", null, new { @class = "btn back-btn" })
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  }
</body>
```
```
CSS:
/*Outter Form*/
.pro-container {
    padding: 20px;
    margin: 0 auto;
    margin-bottom: 50px;
    border-radius: 10px;
    width: 60%;
    background-color: var(--main-color);
}

/*Inner Form*/
.pro-inner {
    padding: 20px;
    border-radius: 10px;
    background-color: var(--light-color);
}

/*Submit Button*/
.pro-create {
    background-color: var(--secondary-color);
    font-weight: bold;
    border-radius: 30px;   
    
}

/*Cancel button*/
.back-btn {
    background-color: var(--main-color--light);
    font-weight: bold;
    border-radius: 30px;  
    
}

.pro-create:hover {    
    background-color: var(--secondary-color--dark);
}


.back-btn:hover {
    background-color: var(--main-color);
}

/*Production highlight*/
.pro-high {
    background-color: var(--secondary-color--dark); 
}

.pro-high:focus {
    border: 3px solid var(--main-color);
}
```
![Create and Edit Style](https://user-images.githubusercontent.com/80483521/120878366-efd77b80-c589-11eb-9433-22378cb51866.PNG)
