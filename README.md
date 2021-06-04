# CSharp-LiveProject
#### The Tech Academy C# Live Project
# Introduction
#### During this project, I worked with a team of other developers to create a dyanmic website using ASP .Net MVC and Entity Framework. While working on the code itself, I also gained experience utilizing the agile methodology for software development. We participated in daily stand-up meetings where we discussed the trials we were facing and what we were working on until the next meeting. We also had a code retrospective at the end of our 10 day sprint where we discussed our successes and areas in which we needed to improve. This project was intended to give us a live experienc of being part of a development team, and I feel that the experience gained during this two weeks will be invaluable in my career.
The website is still in its early stages and isn't fully available. I'll post code snippets from my work below instead.
# Creating a Production class model.
#### The first story I worked on was creating a entity model for a production class and also create to create the CRUD pages for that model.
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
#### My second story I had to create a card layout for the production model and also styled it.

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
# Add pagination and search bar
#### My final story I had to add pagination and a search bar to the Production index page.

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
