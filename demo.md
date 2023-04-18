This hour long workshop is a preview for a longer 3+ hour workshop on [data visualization in python](https://carpentries-incubator.github.io/python-interactive-data-visualizations/).  In the longer workshop, learners
follow along developing all the code from scratch.  Since this is a shorter workshop, instead in this workshop we will start 
with some existing code and then will made some changes to add more interactivity to our plot.

## Setup 
We are going to be creating [this interactive plot](https://sstevens2-interact-with-gapminder-data-app-app-zc78i8.streamlit.app/)
using a tool called streamlit that allows us to create interactive dashboards created with python that allow users to choose what is displayed. [For UW Audiences] there is also a [version published at UW](https://data-viz.it.wisc.edu/test-streamlit-app/).

The example plot is published to the web but typically we would develop and test the application on our local 
computer before deploying it to the web.
Instead of having you all install the software needed to follow-along on your computers, 
we are going to be using a cloud development platform called gitpod where you can create 
a copy of the environment to work in and preview the application.

Here is the repository we will be working with, which includes directions for getting gitpod setup - 
<https://github.com/sstevens2/20220602-ITProConf-dataviz>.

- You will need to sign-in to GitHub or create an account.
- Then, click the "Open in Gitpod" button, recommend right clicking and choosing open in new tab so you have the directions below still in a tab.
- You will get screen asking if you want to continue with GitHub credentials.
- Click "Continue" (or create your own account if you don't want to use your GitHub credentials but that may take longer)
- You will get pop-up about authorizing Gitpod to connect with GitHub.
- Choose the "Authorize gitpod-io" option if you are comfortable with this extension and want to follow along.
- Might get a pop up about authorizing workspace (this is new from the last time I ran it).
- The select editor option will pop up, choose VS Code - BROWSER option.
- - Leave "Latest Release" unchecked and click green "Continue" button.
- Check that the terminal is running and says "You can now view your Streamlit app in your browser.  If it doesnt,  type `streamlit run app.py` in terminal tab.
<!--- Changed? - On the left-hand side click the "Remote Explorer" option, looks like a computer monitor with a circle containing >< in the lower right of the icon. -->
- On the bottom panel, choose the "Ports" tab.
- You should see the Port 8501 listed, next to the address the icon with two boxes and a magnifiying glass for side-by-side display. You may want to choose the browser icon to open in new tab instead but the instructor will need to choose side by side to avoid switching between windows.
- Open `app.py` using the explorer sidebar and then arrange it side by side with the browser view.
- You may want to close the bottom panel and sidebar for better display.

## Developing the app

We want to make a plot using the gapminder data we have.
We can open the data by clicking it in the file pane.
The data is in tidy and long format - we cover how to reformat the data into this form in the full workshop.
Let's take a look at the code of the base app.
<!--- ADD IN MORE ABOUT WHAT EACH LINE DOES HERE FOR FULL DESCRIPTION -->

Now that we have seen that these the streamlit (st) functions add info to the application, 
let's add a line after the plot which describes the plot above using [the streamlit cheat sheet](https://docs.streamlit.io/library/api-reference).

```python
# Line of text describing the plot 
st.markdown("This plot shows the GDP Per Capita for countries in Oceania.")
```

Then we can save this change to our app and reload the application to see the change.

Now let's try something similar as a challenge - 
```python
# Challenge - Display the data table used to create the plot
st.dataframe(df_filtered)
```

And again we will save the change and rerun the app.

<!--- CHECKPOINT: app2 VERSION complete here -->

Now we want to make our subsetting query more flexible so we can create streamlit 
widget that allow the user to choose that is displayed.  
We will do so using formatted strings (f-strings) in python which allow us to substitute variables into strings easily.

```python
# filter the data to only the data we are plotting
continent = "Oceania"
metric = "gdpPercap"
df_filtered = df.query(f"continent=='{continent}' & metric=='{metric}'")
```

When we save and rerun the application, nothing should change.

Next, we can change the data displayed by changing the variables

```python
continent = "Europe"
metric = "pop"
```

This time when we save and reload it changes the data displayed.

However, this looks odd because it didn't change the title and axis labels!
Let's format those with fstrings as well.

```python
title = f"{metric} for countries in {continent}"
fig = px.line(df_filtered, x = "year", y = "value", color = "country", title = title, labels={"value": f"{metric}"})
```

We can make these labels look a bit nicer using a formatting dictionary but for time we will keep them as is.
Instead, let's try a challenge where you update the description to use a formatted string.

```python
# Challenge - update the plot description to use formatted strings.
st.markdown(f"This plot shows the {metric} for countries in {continent}.")
```

Let's try adding a widget to allow the user to select which continent is displayed in the graph.
Looking at the cheatsheet again, we want to use the selectbox input function where users can 
select the continent from a dropdown list of options.

First we need to get the list of possible continents

```python
continent_list = list(df['continent'].unique())
```

Next we need to update the continent variable to use the widget aka the user's input.

```python
continent = st.selectbox(label = "Choose a continent", options = continent_list)
```

Now let's rerun the app and see what changes. 
Now we have a dropdown menu that we can use to change the continent.

Now try it on your own and make a drop down box for choosing the metric.

```python
# Challenge - add a select box to choose the metric
metric_list = list(df['metric'].unique())
metric = st.selectbox(label = "Choose a metric", options = metric_list)
```
Now we have two drop down boxes when we save and rerun.

<!--- CHECKPOINT: app3 VERSION complete here -->

Next we can move our widgets to the sidebar instead of displaying at the top of the application.
In addition to adding the selectboxes to the sidebar, we will also add some text about configuring the plot.

```python
 with st.sidebar:
    st.subheader("Configure the plot")
    continent = st.selectbox(label = "Choose a continent", options = continent_list)
    metric = st.selectbox(label = "Choose a metric", options = metric_list)
```

Now our app has a collapsable sidebar.

Final challenge, try adding a checkbox widget that turns on and off displaying the data at the bottom

```python
# Challenge add a checkbox widget to the sidebar of the app that controls if the data table displays or not.
show_data = st.checkbox(label = "Show the data used to generate this plot", value = False)
```

```python
# Add if statement to the data display
if show_data:
    st.dataframe(df_filtered)
```

Now if we save and rerun the app we can try out the checkbox

<!--- app4 VERSION complete here -- >

There are many more additions we can make to this dashboard - fixing the labels, creating sliders so the 
user can zoom change the years displayed, adding an option for the users to search and add or remove specific 
countries.  We will also want to deploy the application so that others can view it online, remember we have 
been previewing it in our cloud development environment but it isn't deployed for others. Come to the full 
workshop to learn more! - We usually run this workshop during the Fall semester as a mini-workshop - https://datascience.wisc.edu/training-resources/
You can see the final version of the application - ,https://share.streamlit.io/sstevens2/interact-with-gapminder-data-app/main/app.py. 
deployed online for other's to use with streamlit.io.
Note: you can also publish streamlit apps on Posit Connect hosted by DoIT's RCI group.
