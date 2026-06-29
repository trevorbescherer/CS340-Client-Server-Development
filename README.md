# CS340-Client-Server-Development
Grazioso Salvare Rescue Animal Dashboard 

CS 340 – Client/Server Development – Project Two 

About the Project 

This project is a full-stack web application dashboard built for Grazioso Salvare, an international search-and-rescue dog training organization. The dashboard connects to a MongoDB database containing outcome records from the Austin Animal Center and gives Grazioso Salvare staff an interactive way to browse, filter, and visualize animal shelter data to identify dogs that are good candidates for rescue training. 

The dashboard is built with the Dash framework in Python and runs inside a Jupyter notebook. It includes an interactive, sortable, and filterable data table; a set of radio button filters for Water Rescue, Mountain or Wilderness Rescue, and Disaster or Individual Tracking dog profiles; a pie chart showing the breed distribution of the currently filtered data; and a geolocation map showing where a selected animal is located. 

Motivation 

Grazioso Salvare partners with a non-profit agency operating five animal shelters around Austin, Texas, and needs a faster way to identify shelter dogs suited for search-and-rescue training. Different rescue specialties call for different breeds, sexes, and age ranges, and manually searching through thousands of shelter records for the right combination of traits is slow and error-prone. This dashboard exists to automate that search: a staff member can select a rescue type and immediately see a filtered, visualized list of matching candidate dogs pulled directly from the live database. 

Getting Started 

To get a local copy of this dashboard up and running, follow these steps: 

Place ProjectTwoDashboard.ipynb, animal_shelter.py, and the Grazioso Salvare logo image file in the same working directory. 

Ensure the Austin Animal Center Outcomes dataset has already been imported into a MongoDB collection named animals inside a database named aac. 

Open ProjectTwoDashboard.ipynb in JupyterLab and run the single code cell from top to bottom. 

The dashboard will render inline in the notebook output, directly below the code cell. 

Installation 

The following tools and Python packages are required to run this project: 

Python 3 

MongoDB (Community Server) with the Austin Animal Center dataset imported 

PyMongo (pymongo package, used by the CRUD module to connect to MongoDB) 

JupyterLab / Jupyter Notebook 

jupyter-dash (provides JupyterDash, which lets a Dash app render inline inside a notebook cell) 

dash and dash-leaflet (dashboard layout, components, and the interactive geolocation map) 

plotly (renders the pie chart) 

pandas (used throughout to move data between MongoDB query results and the dashboard components) 

Usage 

Once running, a user opens the dashboard and sees the full, unfiltered shelter dataset. Selecting one of the four radio button options immediately re-queries MongoDB and updates the data table, the breed pie chart, and the geolocation map together, so all three widgets always reflect the same filtered view of the data. 

Code Example 

The core of the filtering logic is a dictionary that maps each rescue type to the MongoDB query built from Grazioso Salvare's Rescue Type and Preferred Dog Breeds Table, and a callback that runs the selected query through the CRUD module's read() method: 

RESCUE_QUERIES = {     "Water": {         "animal_type": "Dog",         "breed": {"$in": ["Labrador Retriever Mix", "Chesapeake Bay Retriever", "Newfoundland"]},         "sex_upon_outcome": "Intact Female",         "age_upon_outcome_in_weeks": {"$gte": 26, "$lte": 156}     },     ... }  @app.callback(Output('datatable-id', 'data'), [Input('filter-type', 'value')]) def update_dashboard(filter_type):     query = {} if filter_type == 'Reset' else RESCUE_QUERIES[filter_type]     filtered_df = pd.DataFrame.from_records(db.read(query))     ...     return filtered_df.to_dict('records') 

Tests 

The dashboard was tested manually by running the notebook in JupyterLab and exercising every interactive option: selecting each of the three rescue type filters and the Reset option in turn, and confirming that the data table, pie chart, and geolocation map all updated consistently and correctly with each selection. The screenshots in the Screenshots section below were captured during this manual testing process and document each state. 

Screenshots 

The screenshots below show the dashboard's starting (unfiltered) state, followed by each of the three rescue type filters applied in turn, and finally the Reset option returning the dashboard to its original unfiltered state. 

Starting State 

 

Figure 1a: Starting state — header, logo, and unique identifier 

 

Figure 1b: Starting state — unfiltered interactive data table 

 

Figure 1c: Starting state — unfiltered pie chart and geolocation map 

Water Rescue Filter 

 

Figure 2a: Water Rescue filter — data table 

 

Figure 2b: Water Rescue filter — pie chart and geolocation map 

Mountain or Wilderness Rescue Filter 

 

Figure 3a: Mountain or Wilderness Rescue filter — data table 

 

Figure 3b: Mountain or Wilderness Rescue filter — pie chart and geolocation map 

Disaster or Individual Tracking Filter 

 

Figure 4a: Disaster or Individual Tracking filter — data table 

 

Figure 4b: Disaster or Individual Tracking filter — pie chart and geolocation map 

Reset 

 

Figure 5a: Reset — data table returned to unfiltered view 

 

Figure 5b: Reset — pie chart and geolocation map returned to unfiltered view 

Tools Used and Rationale 

MongoDB 

MongoDB was used as the model component of this application because it stores records as flexible, JSON-like documents, which maps naturally onto the Austin Animal Center Outcomes dataset where not every animal record has the exact same fields filled in. MongoDB's PyMongo driver lets the Python dashboard query the database directly using Python dictionaries as filter criteria, which keeps the model layer simple: the same CRUD module built in Project One, with its create, read, update, and delete methods, is reused unchanged as the data access layer for the dashboard. MongoDB's query operators, such as $in for matching any of several breed names and $gte and $lte for matching an age range, made it straightforward to translate Grazioso Salvare's rescue criteria directly into database queries without needing any additional data transformation layer. 

Dash 

Dash provides the view and controller structure for this web application. Dash is a Python framework that lets a developer build a fully interactive web dashboard without writing separate HTML, CSS, or JavaScript. The dashboard's layout, including the data table, radio button filters, pie chart, and map, is defined declaratively in Python using Dash's html and dcc components, which serves as the view. The interaction between components is handled through Dash's callback decorators, which serve as the controller: whenever the value of the radio button filter changes, Dash automatically calls the connected Python function, which queries MongoDB through the CRUD module and returns updated data to the table, chart, and map. The jupyter-dash package was used specifically because it allows this Dash application to render directly inside a Jupyter notebook cell using mode='inline', which was necessary in this project's Codio environment. 

dash-leaflet and Plotly Express 

dash-leaflet was used to build the geolocation chart, since it provides a Dash-compatible wrapper around the Leaflet.js mapping library and supports markers, tooltips, and popups out of the box. Plotly Express was used for the second chart, a pie chart of breed distribution, because it integrates directly with Dash's dcc.Graph component and can regenerate a chart from a pandas DataFrame in a single line of code. 

Steps Taken to Complete the Project 

Reused the AnimalShelter CRUD Python module from Project One as the data access layer, unchanged. 

Carried over the unfiltered data table and the row-selection geolocation map built during the Module Six Milestone into ProjectTwoDashboard.ipynb. 

Reviewed the Dashboard Specifications Document, including the Rescue Type and Preferred Dog Breeds Table, to determine the exact breed, sex, and age criteria needed for each filter. 

Built a RESCUE_QUERIES dictionary translating that table directly into MongoDB query syntax for Water Rescue, Mountain or Wilderness Rescue, and Disaster or Individual Tracking. 

Added radio button interactive filtering options, including a Reset option, using Dash Core Components. 

Connected the radio buttons to the data table through a Dash callback that re-runs the appropriate MongoDB query and returns the filtered records. 

Added a pie chart of breed distribution using Plotly Express, driven by the data table's derived_virtual_data so it always reflects the current filter. 

Added the Grazioso Salvare logo, linked to www.snhu.edu, and a unique identifier with the developer's name to the dashboard layout to satisfy the branding requirements. 

Tested all four interactive options in JupyterLab and captured screenshots of each state for this README. 

Challenges 

One challenge encountered was a mismatch between the filename referenced in the code and the actual filename of the Grazioso Salvare logo file in the Codio environment, which included spaces rather than underscores. This caused a FileNotFoundError when the dashboard first ran. The issue was resolved by checking the file's exact name in the Codio file browser and updating the image_filename variable in the code to match it exactly. 

A second challenge was viewing the running dashboard inside Codio. Running the app with the default app.run_server() printed a proxy URL, but opening that URL produced a 405 error rather than displaying the dashboard, since Codio's proxy does not always handle a direct link to the Dash server cleanly. This was resolved by switching to app.run_server(mode='inline'), which renders the dashboard directly inside the Jupyter notebook's cell output instead of relying on Codio's external proxy link at all. 

Contact 

Your name: Trevor Bescherer 

CS 340 – Client/Server Development, Southern New Hampshire University 
