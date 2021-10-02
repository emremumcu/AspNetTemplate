# Returning Result from Actions
https://geeksarray.com/blog/return-different-types-of-content-from-asp-net-core-mvc-action-result

``` csharp
return View();
return Content("Index");
return Content("<h1>Index</h1>", "text/html");
return new EmptyResult();
return Json(DateTime.Now);
return StatusCode(200, "123");
return Json(new { Id = 123, Name = "Hero" });
return new JsonResult(new { Id = 123, Name = "Hero" }) { StatusCode = Microsoft.AspNetCore.Http.StatusCodes.Status201Created };
return Ok(new { name = "Fabio", age = 42, gender = "M" });
return StatusCode((int)System.Net.HttpStatusCode.OK, Json("Sucess !!!"));
return new PartialViewResult { ViewName = "_AuthorPartialRP", ViewData = ViewData, };

string content = "...";
ContentResult result = new ContentResult();
result.Content = content;
result.ContentType = "text/plain";
result.StatusCode = (int)HttpStatusCode.OK;
return  result;
```

# Partial views in ASP.NET Core
https://docs.microsoft.com/en-us/aspnet/core/mvc/views/partial?view=aspnetcore-5.0

A partial view is a Razor markup file (.cshtml) without an @page directive that renders HTML output within another markup file's rendered output.

The term partial view is used when developing either an MVC app, where markup files are called views, or a Razor Pages app, where markup files are called pages. This topic generically refers to MVC views and Razor Pages pages as markup files.

In ASP.NET Core MVC, a controller's ViewResult is capable of returning either a view or a partial view. In Razor Pages, a PageModel can return a partial view represented as a PartialViewResult object.

Unlike MVC view or page rendering, a partial view doesn't run _ViewStart.cshtml.

Partial view file names often begin with an underscore (_). This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.

## Partial Tag Helper:

The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax. When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view.

``` html
<partial name="_PartialName" />
<partial name="_PartialName.cshtml" />
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
<partial name="../Account/_PartialName.cshtml" />
```

## Asynchronous HTML Helper

When using an HTML Helper, the best practice is to use PartialAsync. PartialAsync returns an IHtmlContent type wrapped in a Task<TResult>. When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view.

``` html
@await Html.PartialAsync("_PartialName")
@await Html.PartialAsync("_PartialName.cshtml")
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
``` 

Alternatively, you can render a partial view with RenderPartialAsync. This method doesn't return an IHtmlContent. It streams the rendered output directly to the response. Because the method doesn't return a result, it must be called within a Razor code block.

``` html
@{await Html.RenderPartialAsync("_AuthorPartial");}
```

Partial and RenderPartial are the synchronous equivalents of PartialAsync and RenderPartialAsync, respectively. The synchronous equivalents aren't recommended because there are scenarios in which they deadlock. The synchronous methods are targeted for removal in a future release.

## Partial view discovery
When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:

### Razor Pages

* `Currently executing page's folder`
* `Directory graph above the page's folder`
* `/Shared`
* `/Pages/Shared`
* `/Views/Shared`

### MVC

* `/Areas/<Area-Name>/Views/<Controller-Name>`
* `/Areas/<Area-Name>/Views/Shared`
* `/Views/Shared`
* `/Pages/Shared`

The following conventions apply to partial view discovery:

* Different partial views with the same file name are allowed when the partial views are in different folders.
* When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the Shared folder, the partial view in the caller's folder supplies the partial view. If the partial view isn't present in the caller's folder, the partial view is provided from the Shared folder. Partial views in the Shared folder are called shared partial views or default partial views.
* Partial views can be chained—a partial view can call another partial view if a circular reference isn't formed by the calls. Relative paths are always relative to the current file, not to the root or parent of the file.

# Access data from partial views

When a partial view is instantiated, it receives a copy of the parent's ViewData dictionary. Updates made to the data within the partial view aren't persisted to the parent view. ViewData changes in a partial view are lost when the partial view returns.

The following example demonstrates how to pass an instance of ViewDataDictionary to a partial view:

``` html
@await Html.PartialAsync("_PartialName", customViewData)
```

You can pass a model into a partial view. The model can be a custom object. You can pass a model with PartialAsync (renders a block of content to the caller) or RenderPartialAsync (streams the content to the output):

``` html
@await Html.PartialAsync("_PartialName", model)

await Html.PartialAsync("PartialViewName", Model, 
	new ViewDataDictionary(ViewData) {
		{ "index", index } 
	});
```

``` html

```