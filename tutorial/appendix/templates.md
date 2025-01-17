# Dashboard Layouts
In this section we provide you with a series of layout templates as a blueprint for your own dashboards. For this purpose we omit any unecessary functionality by implementing only the layout without any use of callbacks. We will provide templates that combine control panels and graphs as well as templates that use navigation elements like tabs or a sidebar. Finally, we will restructure these dashboards into multi page apps. We encourage you to use these templates when creating your own dashboards. 

## Control panels and graphs
This first section gives you two examples that combine control panels with graphs. You will find:

- A template with control panel on the left
- A template with control panel on the bottom

### Control panel on the left

![Template 1](./template-1.png)

````{dropdown} See the code
    :container: + shadow
    :title: bg-primary text-white font-weight-bold
  
```
# Import packages
from dash import Dash, dcc, html
import dash_bootstrap_components as dbc
import pandas as pd
import plotly.express as px

# Initialise the App
app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

# Data
df = px.data.stocks()
df["date"] = pd.to_datetime(df["date"], format="%Y-%m-%d")

# Figure
fig = px.line(
    df, x="date", y=["AAPL"], template="plotly_white", title="My Plotly Graph"
)

# Title
title = dcc.Markdown("My Dashboard", className="bg-light", style={"font-size": 30})

# Dropdown
dropdown = dcc.Dropdown(options=["AAPL", "GOOG", "MSFT"], value="AAPL")

# Date Picker
date_range = dcc.DatePickerRange(
    start_date_placeholder_text="start date",
    end_date_placeholder_text="end date",
    min_date_allowed=df.date.min(),
    max_date_allowed=df.date.max(),
    display_format="DD-MMM-YYYY",
    first_day_of_week=1,
)

# Checklist
checklist = dbc.Checklist(
    options=[{"label": "Dark theme", "value": 1}],
    value=[],
    switch=True,
)

# Radio items
radio_items = dbc.RadioItems(
    options=[
        {"label": "Red", "value": 0},
        {"label": "Green", "value": 1},
        {"label": "Blue", "value": 2},
    ],
    value=2,
    inline=True,
)

# Card content
card_content = [
    dbc.CardBody(
        [
            html.H5("My Control Panel"),
            html.P(
                "1) Select an option of the dropdown",
            ),
            dropdown,
            html.Br(),
            html.P(
                "2) Pick a date range from the form",
            ),
            date_range,
            html.Br(),
            html.Hr(),
            html.H6("Optional input"),
            html.P(
                "3) Enable dark theme of your graph",
            ),
            checklist,
            html.Br(),
            html.P(
                "4) Change the color of the graphs line",
            ),
            radio_items,
        ]
    ),
]

# App Layout
app.layout = html.Div(
    [
        dbc.Row([dbc.Col([title], style={"text-align": "center", "margin": "auto"})]),
        html.Br(),
        dbc.Container(
            [
                dbc.Row(
                    [
                        dbc.Col(
                            [
                                dbc.Row(dbc.Card(card_content, color="light")),
                            ],
                            width=4,
                        ),
                        dbc.Col(
                            dbc.Card(
                                dcc.Graph(id="figure1", figure=fig), color="light"
                            ),
                            width=8,
                        ),
                    ]
                )
            ]
        ),
    ]
)


# Run the App
if __name__ == "__main__":
    app.run_server(debug=False)
```
````

### Control panel on the bottom

![Template 2](./template-2.png)

````{dropdown} See the code
    :container: + shadow
    :title: bg-primary text-white font-weight-bold

```
# Import packages
from dash import Dash, dcc, html
import dash_bootstrap_components as dbc
import plotly.express as px

# Initialise the App
app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

# Data
df = px.data.gapminder()
df_options = df[df["continent"].isin(["Europe"])]["country"].unique()
df = df[df["country"].isin(["Spain", "United Kingdom"])]

# Figure
fig = px.bar(
    df,
    x="year",
    y="lifeExp",
    color="country",
    barmode="group",
    title="Grouped Bar Chart",
    template="plotly_white",
)
fig.update_yaxes(range=[60, 80])

# Title
title = dcc.Markdown("My Dashboard", className="bg-light", style={"font-size": 30})

# Dropdown
dropdown = dcc.Dropdown(
    options=df_options, value=["Spain", "United Kingdom"], multi=True
)

# Slider
slider = dcc.Slider(
    df["year"].min(),
    df["year"].max(),
    5,
    value=df["year"].min() + 10,
    marks=None,
    tooltip={"placement": "bottom", "always_visible": True},
)

# Radio items
radio_items = dbc.RadioItems(
    options=[
        {"label": "Option A", "value": 0},
        {"label": "Option B", "value": 1},
        {"label": "Option C", "value": 2},
    ],
    value=0,
    inline=True,
)

# Card content
card_content = [
    dbc.CardHeader("Card header"),
    dbc.CardBody(
        [
            html.H5("Card title"),
            html.P(
                "Here you might want to add some statics or further information for your dashboard",
            ),
        ]
    ),
]

# App Layout
app.layout = html.Div(
    [
        dbc.Row([dbc.Col([title], style={"text-align": "center", "margin": "auto"})]),
        html.Br(),
        dbc.Row(
            [
                dbc.Col(
                    [dbc.Card(dcc.Graph(id="figure1", figure=fig), color="light")],
                    width=10,
                    style={"margin": "auto"},
                ),
            ]
        ),
        html.Br(),
        dbc.Row(
            [
                dbc.Col(
                    [
                        dbc.Row(
                            [
                                dbc.Col(
                                    [
                                        html.P("Select the countries to display"),
                                        dropdown,
                                    ]
                                ),
                                dbc.Col(
                                    [
                                        html.P("Select a year"),
                                        slider,
                                    ]
                                ),
                                dbc.Col(
                                    [
                                        html.P("Choose one more option"),
                                        radio_items,
                                    ]
                                ),
                            ]
                        ),
                    ],
                    width=10,
                    style={"margin": "auto"},
                )
            ]
        ),
        html.Br(),
        dbc.Row(
            [
                dbc.Col(
                    [
                        dbc.Row(
                            [
                                dbc.Col([dbc.Card(card_content, color="light")]),
                                dbc.Col([dbc.Card(card_content, color="light")]),
                                dbc.Col([dbc.Card(card_content, color="light")]),
                            ]
                        )
                    ],
                    width=10,
                    style={"margin": "auto"},
                ),
            ]
        ),
    ]
)


# Run the App
if __name__ == "__main__":
    app.run_server(debug=False)
```

````

## Navigation panels and graphs
This second section provides two examples of how to combine navigation panels and graphs. In particular, you will find:

- A template with navigation tabs on the top
- A template with a navigation bar on the side

### Navigation tabs on top

![Template 3](./template-3.png)

````{dropdown} See the code
    :container: + shadow
    :title: bg-primary text-white font-weight-bold

```
# Import packages
from dash import Dash, dcc, html
import dash_bootstrap_components as dbc
import plotly.express as px

# Initialise the App
app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

# Data
df = px.data.gapminder()
df_2007 = df[df.year == 2007]
continents = df["continent"].unique()

# Title
title = dcc.Markdown("My Dashboard", className="bg-light", style={"font-size": 30})

# Tabs
list_of_tabs = [
    dbc.Tab(
        dbc.Card(
            [
                dbc.CardBody(
                    [
                        html.H5(
                            "Analysis on Life Expectation / GDP per Capita in 2007"
                        ),
                        dcc.Graph(
                            figure=px.scatter(
                                df_2007,
                                x="gdpPercap",
                                y="lifeExp",
                                color="continent",
                                size="pop",
                                size_max=60,
                            )
                        ),
                    ]
                )
            ]
        ),
        label="World",
        activeLabelClassName="bg-light",
    )
]

for continent in continents:
    list_of_tabs.append(
        dbc.Tab(
            dbc.Card(
                [
                    dbc.CardBody(
                        [
                            html.H5(
                                "Analysis on Life Expectation / GDP per Capita in 2007 for {}".format(
                                    continent
                                )
                            ),
                            dcc.Graph(
                                figure=px.scatter(
                                    df_2007[df_2007["continent"] == continent],
                                    x="gdpPercap",
                                    y="lifeExp",
                                    color="country",
                                    size="pop",
                                    size_max=60,
                                )
                            ),
                        ]
                    )
                ]
            ),
            label=continent,
            activeLabelClassName="bg-light",
        )
    )

# App Layout
app.layout = dbc.Container(
    [
        dbc.Row([
            dbc.Col([title], width=12, style={"text-align": "center", "margin": "auto"})
        ]),

        dbc.Tabs(list_of_tabs)
    ]
)


# Run the App
if __name__ == "__main__":
    app.run_server(debug=False)
```

````

There are multiple ways of switching your navigation from top to the side of your graph. Using the tabs component from the dash core components, they come with a property called `vertical`, see also the [official documentation](https://dash.plotly.com/dash-core-components/tabs). Another option is a navigation bar that you will see below.

### Navigation sidebar

![Template 4](./template-4.png)

````{dropdown} See the code
    :container: + shadow
    :title: bg-primary text-white font-weight-bold

```
# Import packages
import dash
import dash_bootstrap_components as dbc
from dash import Input, Output, dcc, html, ctx
import plotly.express as px

# Initialise the App
app = dash.Dash(external_stylesheets=[dbc.themes.BOOTSTRAP])

# Data
df = px.data.gapminder()
df_2007 = df[df.year == 2007]
continents = df["continent"].unique()

# Styling
SIDEBAR_STYLE = {
    "position": "fixed",
    "top": 0,
    "left": 0,
    "bottom": 0,
    "width": "16rem",
    "padding": "2rem 1rem",
    "background-color": "#f8f9fa",
}
CONTENT_STYLE = {
    "margin-left": "18rem",
    "margin-right": "2rem",
    "padding": "2rem 1rem",
}

# Sidebar
sidebar = html.Div(
    [
        html.H2("Sidebar", className="display-4"),
        html.Hr(),
        html.P("A simple sidebar layout with navigation links", className="lead"),
        dbc.Button(continents[0], color="light", n_clicks=0, id=continents[0]),
        html.Hr(),
        dbc.Button(continents[1], color="light", n_clicks=0, id=continents[1]),
        html.Hr(),
        dbc.Button(continents[2], color="light", n_clicks=0, id=continents[2]),
        html.Hr(),
        dbc.Button(continents[3], color="light", n_clicks=0, id=continents[3]),
        html.Hr(),
        dbc.Button(continents[4], color="light", n_clicks=0, id=continents[4]),
    ],
    style=SIDEBAR_STYLE,
)

# Title
title = dcc.Markdown("My Dashboard", className="bg-light", style={"font-size": 30})

# Content
content = html.Div(id="page-content", style=CONTENT_STYLE)

# App Layout
app.layout = html.Div(
    [
        dbc.Row([dbc.Col([title], style={"text-align": "center", "margin": "auto"})]),
        sidebar,
        content,
    ]
)


# Configure callbacks
@app.callback(
    Output("page-content", "children"),
    Input(continents[0], "n_clicks"),
    Input(continents[1], "n_clicks"),
    Input(continents[2], "n_clicks"),
    Input(continents[3], "n_clicks"),
    Input(continents[4], "n_clicks"),
)
def update_page(n1, n2, n3, n4, n5):
    if ctx.triggered_id in continents:
        return dbc.Container(
            [
                dbc.Card(
                    [
                        dbc.CardBody(
                            [
                                html.H5(
                                    "Analysis on Life Expectation / GDP per Capita in 2007 for {}".format(
                                        ctx.triggered_id
                                    )
                                ),
                                dcc.Graph(
                                    id="graph {}".format(ctx.triggered_id),
                                    figure=px.scatter(
                                        df_2007[
                                            df_2007["continent"] == ctx.triggered_id
                                        ],
                                        x="gdpPercap",
                                        y="lifeExp",
                                        color="country",
                                        size="pop",
                                        size_max=60,
                                    ),
                                ),
                            ]
                        )
                    ]
                )
            ]
        )
    else:
        return dbc.Container(
            [
                dbc.Card(
                    [
                        dbc.CardBody(
                            [
                                html.H5(
                                    "Analysis on Life Expectation / GDP per Capita in 2007"
                                ),
                                dcc.Graph(
                                    figure=px.scatter(
                                        df_2007,
                                        x="gdpPercap",
                                        y="lifeExp",
                                        color="continent",
                                        size="pop",
                                        size_max=60,
                                    )
                                ),
                            ]
                        )
                    ]
                )
            ]
        )


# Run the App
if __name__ == "__main__":
    app.run_server(debug=False)
```

````

## Combining navigation and control panels
The third section provides you with two examples that are combining the upper dashboards to a more complex dashboard. You will see

- A template with control panels and navigation on the top and
- A template with control panels and navigation on the side

### Template with control panels and navigation on the top

![Template 5](./template-5.png)

````{dropdown} See the code
    :container: + shadow
    :title: bg-primary text-white font-weight-bold
  
```
# Import packages
import dash
import dash_bootstrap_components as dbc
from dash import dcc, html
import pandas as pd
import plotly.express as px

# Initialise the App
app = dash.Dash(external_stylesheets=[dbc.themes.BOOTSTRAP])

#################
# 1 # Bar plot ##
#################

# Data
df_ba = px.data.gapminder()
df_options = df_ba[df_ba["continent"].isin(["Europe"])]["country"].unique()
df_ba = df_ba[df_ba["country"].isin(["Spain", "United Kingdom"])]

# Figure
fig_ba = px.bar(
    df_ba,
    x="year",
    y="lifeExp",
    color="country",
    barmode="group",
    title="Grouped Bar Chart",
    template="plotly_white",
)
fig_ba.update_yaxes(range=[60, 80])

# Dropdown
dropdown_ba = dcc.Dropdown(
    options=df_options, value=["Spain", "United Kingdom"], multi=True
)

# Slider
slider_ba = dcc.Slider(
    df_ba["year"].min(),
    df_ba["year"].max(),
    5,
    value=df_ba["year"].min() + 10,
    marks=None,
    tooltip={"placement": "bottom", "always_visible": True},
)

# Radio items
radio_items_ba = dbc.RadioItems(
    options=[
        {"label": "Option A", "value": 0},
        {"label": "Option B", "value": 1},
        {"label": "Option C", "value": 2},
    ],
    value=0,
    inline=True,
)

# Card content
card_content_ba = [
    dbc.CardHeader("Card header"),
    dbc.CardBody(
        [
            html.H5("Card title"),
            html.P(
                "Here you might want to add some statics or further information for your dashboard",
            ),
        ]
    ),
]

##################
# 2 # Line plot ##
##################

# Data
df_li = px.data.stocks()
df_li["date"] = pd.to_datetime(df_li["date"], format="%Y-%m-%d")

# Figure
fig = px.line(
    df_li, x="date", y=["AAPL"], template="plotly_white", title="My Plotly Graph"
)

# Dropdown
dropdown = dcc.Dropdown(options=["AAPL", "GOOG", "MSFT"], value="AAPL")

# Date Picker
date_range = dcc.DatePickerRange(
    start_date_placeholder_text="start date",
    end_date_placeholder_text="end date",
    min_date_allowed=df_li.date.min(),
    max_date_allowed=df_li.date.max(),
    display_format="DD-MMM-YYYY",
    first_day_of_week=1,
)

# Checklist
checklist = dbc.Checklist(
    options=[{"label": "Dark theme", "value": 1}],
    value=[],
    switch=True,
)

# Radio items
radio_items = dbc.RadioItems(
    options=[
        {"label": "Red", "value": 0},
        {"label": "Green", "value": 1},
        {"label": "Blue", "value": 2},
    ],
    value=2,
    inline=True,
)

# Card content
card_content = [
    dbc.CardBody(
        [
            html.H5("My Control Panel"),
            html.P(
                "1) Select an option of the dropdown",
            ),
            dropdown,
            html.Br(),
            html.P(
                "2) Pick a date range from the form",
            ),
            date_range,
            html.Br(),
            html.Hr(),
            html.H6("Optional input"),
            html.P(
                "3) Enable dark theme of your graph",
            ),
            checklist,
            html.Br(),
            html.P(
                "4) Change the color of the graphs line",
            ),
            radio_items,
        ]
    ),
]

#####################
# 3 # Scatter plot ##
#####################

# Data
df_sc = px.data.gapminder()
df_2007 = df_sc[df_sc.year == 2007]

# Title
title = dcc.Markdown("My Dashboard", className="bg-light", style={"font-size": 30})

# App Layout
app.layout = html.Div(
    [
        dbc.Row([dbc.Col([title], style={"text-align": "center", "margin": "auto"})]),
        dbc.Container(
            [
                dbc.Tabs(
                    [
                        dbc.Tab(
                            [
                                dbc.Card(
                                    [
                                        dbc.CardBody(
                                            [
                                                dbc.Row(
                                                    [
                                                        dbc.Col(
                                                            [
                                                                dbc.Card(
                                                                    dcc.Graph(
                                                                        figure=fig_ba
                                                                    ),
                                                                    color="light",
                                                                )
                                                            ]
                                                        ),
                                                    ]
                                                ),
                                                html.Br(),
                                                dbc.Row(
                                                    [
                                                        dbc.Col(
                                                            [
                                                                html.P(
                                                                    "Select the countries to display"
                                                                ),
                                                                dropdown_ba,
                                                            ]
                                                        ),
                                                        dbc.Col(
                                                            [
                                                                html.P("Select a year"),
                                                                slider_ba,
                                                            ]
                                                        ),
                                                        dbc.Col(
                                                            [
                                                                html.P(
                                                                    "Choose one more option"
                                                                ),
                                                                radio_items_ba,
                                                            ]
                                                        ),
                                                    ]
                                                ),
                                                html.Br(),
                                                dbc.Row(
                                                    [
                                                        dbc.Col(
                                                            [
                                                                dbc.Card(
                                                                    card_content_ba,
                                                                    color="light",
                                                                )
                                                            ]
                                                        ),
                                                        dbc.Col(
                                                            [
                                                                dbc.Card(
                                                                    card_content_ba,
                                                                    color="light",
                                                                )
                                                            ]
                                                        ),
                                                        dbc.Col(
                                                            [
                                                                dbc.Card(
                                                                    card_content_ba,
                                                                    color="light",
                                                                )
                                                            ]
                                                        ),
                                                    ]
                                                ),
                                            ]
                                        )
                                    ]
                                )
                            ],
                            label="Bar plot",
                            activeLabelClassName="bg-light",
                        ),
                        dbc.Tab(
                            [
                                dbc.Card(
                                    [
                                        dbc.CardBody(
                                            [
                                                dbc.Row(
                                                    [
                                                        dbc.Col(
                                                            [
                                                                dbc.Row(
                                                                    dbc.Card(
                                                                        card_content,
                                                                        color="light",
                                                                    )
                                                                ),
                                                            ],
                                                            width=4,
                                                        ),
                                                        dbc.Col(
                                                            dbc.Card(
                                                                dcc.Graph(figure=fig),
                                                                color="light",
                                                            ),
                                                            width=8,
                                                        ),
                                                    ]
                                                )
                                            ]
                                        )
                                    ]
                                )
                            ],
                            label="Line plot",
                            activeLabelClassName="bg-light",
                        ),
                        dbc.Tab(
                            [
                                dbc.Card(
                                    [
                                        dbc.CardBody(
                                            [
                                                html.H3(
                                                    "Analysis on Life Expectation / GDP per Capita in 2007"
                                                ),
                                                dcc.Graph(
                                                    figure=px.scatter(
                                                        df_2007,
                                                        x="gdpPercap",
                                                        y="lifeExp",
                                                        color="continent",
                                                        size="pop",
                                                        size_max=60,
                                                    )
                                                ),
                                            ]
                                        )
                                    ]
                                )
                            ],
                            label="Scatter plot",
                            activeLabelClassName="bg-light",
                        ),
                    ]
                )
            ]
        ),
    ]
)


# Run the App
if __name__ == "__main__":
    app.run_server(debug=False)
```

````

### Template with control panels and navigation on the side

![Template 6](./template-6.png)

````{dropdown} See the code
    :container: + shadow
    :title: bg-primary text-white font-weight-bold
  
```
# Import packages
import dash
import dash_bootstrap_components as dbc
from dash import Input, Output, dcc, html, ctx
import pandas as pd
import plotly.express as px

# Initialise the App
app = dash.Dash(external_stylesheets=[dbc.themes.BOOTSTRAP])

#################
# 1 # Bar plot ##
#################

# Data
df_ba = px.data.gapminder()
df_options = df_ba[df_ba["continent"].isin(["Europe"])]["country"].unique()
df_ba = df_ba[df_ba["country"].isin(["Spain", "United Kingdom"])]

# Figure
fig_ba = px.bar(
    df_ba,
    x="year",
    y="lifeExp",
    color="country",
    barmode="group",
    title="Grouped Bar Chart",
    template="plotly_white",
)
fig_ba.update_yaxes(range=[60, 80])

# Dropdown
dropdown_ba = dcc.Dropdown(
    options=df_options, value=["Spain", "United Kingdom"], multi=True
)

# Slider
slider_ba = dcc.Slider(
    df_ba["year"].min(),
    df_ba["year"].max(),
    5,
    value=df_ba["year"].min() + 10,
    marks=None,
    tooltip={"placement": "bottom", "always_visible": True},
)

# Radio items
radio_items_ba = dbc.RadioItems(
    options=[
        {"label": "Option A", "value": 0},
        {"label": "Option B", "value": 1},
        {"label": "Option C", "value": 2},
    ],
    value=0,
    inline=True,
)

# Card content
card_content_ba = [
    dbc.CardHeader("Card header"),
    dbc.CardBody(
        [
            html.H5("Card title"),
            html.P(
                "Here you might want to add some statics or further information for your dashboard",
            ),
        ]
    ),
]

##################
# 2 # Line plot ##
##################

# Data
df_li = px.data.stocks()
df_li["date"] = pd.to_datetime(df_li["date"], format="%Y-%m-%d")

# Figure
fig = px.line(
    df_li, x="date", y=["AAPL"], template="plotly_white", title="My Plotly Graph"
)

# Dropdown
dropdown = dcc.Dropdown(options=["AAPL", "GOOG", "MSFT"], value="AAPL")

# Date Picker
date_range = dcc.DatePickerRange(
    start_date_placeholder_text="start date",
    end_date_placeholder_text="end date",
    min_date_allowed=df_li.date.min(),
    max_date_allowed=df_li.date.max(),
    display_format="DD-MMM-YYYY",
    first_day_of_week=1,
)

# Checklist
checklist = dbc.Checklist(
    options=[{"label": "Dark theme", "value": 1}],
    value=[],
    switch=True,
)

# Radio items
radio_items = dbc.RadioItems(
    options=[
        {"label": "Red", "value": 0},
        {"label": "Green", "value": 1},
        {"label": "Blue", "value": 2},
    ],
    value=2,
    inline=True,
)

# Card content
card_content = [
    dbc.CardBody(
        [
            html.H5("My Control Panel"),
            html.P(
                "1) Select an option of the dropdown",
            ),
            dropdown,
            html.Br(),
            html.P(
                "2) Pick a date range from the form",
            ),
            date_range,
            html.Br(),
            html.Hr(),
            html.H6("Optional input"),
            html.P(
                "3) Enable dark theme of your graph",
            ),
            checklist,
            html.Br(),
            html.P(
                "4) Change the color of the graphs line",
            ),
            radio_items,
        ]
    ),
]

#####################
# 3 # Scatter plot ##
#####################

# Data
df_sc = px.data.gapminder()
df_2007 = df_sc[df_sc.year == 2007]

# Styling
SIDEBAR_STYLE = {
    "position": "fixed",
    "top": 0,
    "left": 0,
    "bottom": 0,
    "width": "16rem",
    "padding": "2rem 1rem",
    "background-color": "#f8f9fa",
}
CONTENT_STYLE = {
    "margin-left": "18rem",
    "margin-right": "2rem",
    "padding": "2rem 1rem",
}

# Sidebar
sidebar = html.Div(
    [
        html.H2("Sidebar"),
        html.Hr(),
        html.P("A simple sidebar layout with navigation links"),
        dbc.Button("Bar plot", color="light", n_clicks=0, id="Bar plot"),
        html.Hr(),
        dbc.Button("Line plot", color="light", n_clicks=0, id="Line plot"),
        html.Hr(),
        dbc.Button("Scatter plot", color="light", n_clicks=0, id="Scatter plot"),
    ],
    style=SIDEBAR_STYLE,
)

# Title
title = dcc.Markdown("My Dashboard", className="bg-light", style={"font-size": 30})

# Content
content = html.Div(id="page-content", style=CONTENT_STYLE)

# App Layout
app.layout = html.Div(
    [
        dbc.Row([dbc.Col([title], style={"text-align": "center", "margin": "auto"})]),
        sidebar,
        content,
    ]
)

# Configure callbacks
@app.callback(
    Output("page-content", "children"),
    Input("Bar plot", "n_clicks"),
    Input("Line plot", "n_clicks"),
    Input("Scatter plot", "n_clicks"),
)
def update_page(n1, n2, n3):
    if ctx.triggered_id == "Bar plot":
        return dbc.Container(
            [
                dbc.Row(
                    [
                        dbc.Col(
                            [
                                dbc.Card(
                                    dcc.Graph(id="figure1", figure=fig_ba),
                                    color="light",
                                )
                            ]
                        ),
                    ]
                ),
                html.Br(),
                dbc.Row(
                    [
                        dbc.Col(
                            [
                                html.P("Select the countries to display"),
                                dropdown_ba,
                            ]
                        ),
                        dbc.Col(
                            [
                                html.P("Select a year"),
                                slider_ba,
                            ]
                        ),
                        dbc.Col(
                            [
                                html.P("Choose one more option"),
                                radio_items_ba,
                            ]
                        ),
                    ]
                ),
                html.Br(),
                dbc.Row(
                    [
                        dbc.Col([dbc.Card(card_content_ba, color="light")]),
                        dbc.Col([dbc.Card(card_content_ba, color="light")]),
                        dbc.Col([dbc.Card(card_content_ba, color="light")]),
                    ]
                ),
            ]
        )
    elif ctx.triggered_id == "Line plot":
        return dbc.Container(
            [
                dbc.Row(
                    [
                        dbc.Col(
                            [
                                dbc.Row(dbc.Card(card_content, color="light")),
                            ],
                            width=4,
                        ),
                        dbc.Col(
                            dbc.Card(
                                dcc.Graph(id="figure1", figure=fig), color="light"
                            ),
                            width=8,
                        ),
                    ]
                ),
            ]
        )
    elif ctx.triggered_id == "Scatter plot":
        return dbc.Container(
            [
                html.H3("Analysis on Life Expectation / GDP per Capita in 2007"),
                dcc.Graph(
                    figure=px.scatter(
                        df_2007,
                        x="gdpPercap",
                        y="lifeExp",
                        color="continent",
                        size="pop",
                        size_max=60,
                    )
                ),
            ]
        )
    else:
        return dbc.Container(
            [
                html.H5(
                    "Please navigate to an analysed data set with the navigation on the left."
                ),
            ]
        )


# Run the App
if __name__ == "__main__":
    app.run_server(debug=False)
```

````

As the code for this implementation starts to become much longer and more complex the last section will provide you with the complex dashboards built into a multipage app for a better structre. 

## Multipage app
The last section concludes this appendix on dashboard layouts and will wrap everything we have implemented above into a multipage app. Here, you find

- A multipage app with navigation bar on the top and
- A multipage app with navigation bar on the side

Below each template you find the corresponding code to incorporate into your main `app.py` file. The other files are the same as for both dashboards, you can [download them here](./pages.zip?raw=true).

### Multipage app with navigation bar on the top

![Template 7](./template-7.png)

````{dropdown} See the code
    :container: + shadow
    :title: bg-primary text-white font-weight-bold
  
```
# Import packages
from dash import Dash, html
import dash_bootstrap_components as dbc
import dash
from dash_bootstrap_templates import ThemeSwitchAIO

# Configure Themes
theme_toggle = ThemeSwitchAIO(
    aio_id="theme",
    themes=[dbc.themes.DARKLY, dbc.themes.FLATLY],
    icons={"left": "fa fa-sun", "right": "fa fa-moon"},
)
dbc_css = "https://cdn.jsdelivr.net/gh/AnnMarieW/dash-bootstrap-templates/dbc.min.css"

# Initialise the App
app = Dash(
    __name__,
    use_pages=True,
    external_stylesheets=[[dbc.themes.FLATLY, dbc_css], dbc.icons.FONT_AWESOME],
)

# Header
header = dbc.Navbar(
    dbc.Container(
        [
            html.A(
                dbc.Row([dbc.Col(dbc.NavbarBrand("My Dashboard"))], align="center"),
                href="/",
                style={"textDecoration": "none"},
            ),
            dbc.Row(
                [
                    dbc.NavbarToggler(id="navbar-toggler"),
                    dbc.Nav(
                        [
                            dbc.NavLink(page["name"], href=page["path"])
                            for page in dash.page_registry.values()
                            if page["module"] != "pages.not_found_404"
                        ]
                    ),
                ]
            ),
        ],
        fluid=True,
    ),
    dark=True,
    color="dark",
)

# App Layout
app.layout = dbc.Container([header, theme_toggle, dash.page_container], fluid=True)

# Run the App
if __name__ == "__main__":
    app.run_server(debug=False)
```

````

### Multipage app with navigation bar on the side

![Template 8](./template-8.png)

````{dropdown} See the code
    :container: + shadow
    :title: bg-primary text-white font-weight-bold
  
```
# Import packages
from dash import Dash, dcc, html
import dash_bootstrap_components as dbc
import dash
from dash_bootstrap_templates import ThemeSwitchAIO

# Configure Themes
theme_toggle = ThemeSwitchAIO(
    aio_id="theme",
    themes=[dbc.themes.DARKLY, dbc.themes.FLATLY],
    icons={"left": "fa fa-sun", "right": "fa fa-moon"},
)
dbc_css = "https://cdn.jsdelivr.net/gh/AnnMarieW/dash-bootstrap-templates/dbc.min.css"

# Initialise the App
app = Dash(
    __name__,
    use_pages=True,
    external_stylesheets=[[dbc.themes.FLATLY, dbc_css], dbc.icons.FONT_AWESOME],
)

# Styling
SIDEBAR_STYLE = {
    "position": "fixed",
    "top": 0,
    "left": 0,
    "bottom": 0,
    "width": "16rem",
    "padding": "2rem 1rem",
    "border-right": "solid",
}
CONTENT_STYLE = {
    "margin-left": "18rem",
    "margin-right": "2rem",
    "padding": "2rem 1rem",
}

# Sidebar
sidebar = html.Div(
    [
        html.H2("Sidebar"),
        html.P("A simple sidebar layout with navigation links"),
        dbc.Row([theme_toggle]),
        html.Hr(),
        dbc.Row(
            [
                dbc.Navbar(
                    [
                        dbc.NavbarToggler(id="navbar-toggler"),
                        dbc.Nav(
                            [
                                dbc.NavLink(page["name"], href=page["path"])
                                for page in dash.page_registry.values()
                                if page["module"] != "pages.not_found_404"
                            ],
                            vertical=True,
                        ),
                    ],
                    color="dark",
                    dark=True,
                ),
            ]
        ),
    ],
    style=SIDEBAR_STYLE,
)

# Title
title = dcc.Markdown("My Dashboard", style={"font-size": 30})

# Content
content = html.Div(dash.page_container, id="page-content", style=CONTENT_STYLE)

# App Layout
app.layout = html.Div(
    [
        dbc.Row([dbc.Col([title], style={"text-align": "center", "margin": "auto"})]),
        sidebar,
        content,
    ]
)

# Run the App
if __name__ == "__main__":
    app.run_server(debug=False)
```

````
