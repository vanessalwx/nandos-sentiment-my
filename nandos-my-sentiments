import pandas as pd
import plotly.express as px
import dash
from dash import dcc, html, Input, Output
import plotly.graph_objects as go

app = dash.Dash(__name__)
app.title = "Nando's MY Social Intelligence Dashboard"

# --- Malaysian Sentiment Data ---
data = pd.DataFrame({
    "Month": ["Jan", "Feb", "Mar", "Apr", "May"] * 6,
    "Platform": ["Reddit", "TripAdvisor", "Yelp", "Facebook", "Instagram", "Twitter"] * 5,
    "Sentiment": [
        "Negative", "Negative", "Neutral", "Positive", "Neutral", "Positive",
        "Negative", "Negative", "Neutral", "Positive", "Neutral", "Positive",
        "Negative", "Negative", "Neutral", "Positive", "Neutral", "Positive",
        "Negative", "Negative", "Neutral", "Positive", "Neutral", "Positive",
        "Negative", "Negative", "Neutral", "Positive", "Neutral", "Positive"
    ],
    "Mentions": [12, 10, 8, 15, 9, 11, 14, 13, 7, 16, 10, 12, 13, 11, 6, 17, 8, 13, 15, 12, 9, 18, 9, 14, 11, 13, 8, 19, 7, 15]
})

# Top themes (Malaysia)
top_themes = pd.DataFrame({
    "Theme": [
        "Price & Value", "Service Inconsistency", "Flavor Consistency",
        "Campaign Engagement"
    ],
    "Mentions": [45, 33, 28, 40],
    "Sentiment": [
        "Negative", "Negative", "Neutral", "Positive"
    ]
})

# Top quotes (Malaysia context)
top_quotes = pd.DataFrame({
    "Platform": [
        "Reddit", "TripAdvisor", "Yelp", "Facebook", "TripAdvisor"
    ],
    "Quote": [
        "1/4 chicken used to be RM21.90… Now RM26.90 with smaller portion.",
        "The staff is really rude… the worst Nando’s I had been in.",
        "Tasty sauces but inconsistent chicken, ours was over cooked.",
        "Not just a love song about PERi‑PERi. Find hidden codes to win a feast!",
        "My server looked like she was about to cry… inconsistent service."
    ],
    "Sentiment": [
        "Negative", "Negative", "Neutral", "Positive", "Negative"
    ],
    "Campaign": [
        "N/A", "N/A", "N/A", "Innuandos", "N/A"
    ],
    "Theme": [
        "Price & Value", "Service Inconsistency", "Flavor Consistency", "Campaign Engagement", "Service Inconsistency"
    ]
})

# --- Layout ---
app.layout = html.Div([
    html.Div([
        html.Img(src='/assets/nandos_logo.png', style={
            'height': '80px', 'margin': '0 auto', 'display': 'block'})
    ]),

    html.H1("Nando’s MY Social Intelligence Dashboard", style={"textAlign": "center", "color": "#d71f26"}),

    html.Div([
        html.Label("Select Month(s):", style={"fontWeight": "bold"}),
        dcc.Dropdown(
            options=[{"label": m, "value": m} for m in data["Month"].unique()],
            value=["Jan", "Feb", "Mar", "Apr", "May"],
            multi=True,
            id="month-selector"
        ),
        html.Br(),
        html.Label("Select Platform(s):", style={"fontWeight": "bold"}),
        dcc.Dropdown(
            options=[{"label": p, "value": p} for p in data["Platform"].unique()],
            value=data["Platform"].unique().tolist(),
            multi=True,
            id="platform-selector"
        )
    ], style={"width": "60%", "margin": "auto", "backgroundColor": "#f9f9f9", "padding": "20px", "borderRadius": "10px"}),

    html.H2("Mentions Over Time by Sentiment", style={"color": "#d71f26"}),
    dcc.Graph(id="mentions-line"),

    html.H2("Sentiment Breakdown by Platform", style={"color": "#d71f26"}),
    dcc.Graph(id="sentiment-platform-bar"),

    html.H2("Top Discussed Themes", style={"color": "#d71f26"}),
    dcc.Graph(
        figure=px.bar(top_themes, x="Mentions", y="Theme", color="Sentiment", orientation="h",
                     color_discrete_map={"Positive": "#d71f26", "Neutral": "#333333", "Negative": "#999999"},
                     title="Top Themes by Mentions")
    ),

    html.H2("Top Social Quotes", style={"color": "#d71f26"}),
    html.Table([
        html.Thead([
            html.Tr([html.Th(col, style={"backgroundColor": "#d71f26", "color": "white"}) for col in ["Quote", "Sentiment"]])
        ]),
        html.Tbody([
            html.Tr([
                html.Td(
                    html.Span(top_quotes.iloc[i]["Quote"],
                              title=f"Source: {top_quotes.iloc[i]['Platform']} | Campaign: {top_quotes.iloc[i]['Campaign']}"),
                    style={"padding": "8px", "border": "1px solid #ccc"}
                ),
                html.Td(top_quotes.iloc[i]["Sentiment"], style={"padding": "8px", "border": "1px solid #ccc"})
            ]) for i in range(len(top_quotes))
        ])
    ], style={"width": "80%", "margin": "auto", "border": "1px solid #ccc", "borderCollapse": "collapse", "backgroundColor": "#fff"})
], style={"backgroundColor": "#ffffff", "fontFamily": "Arial, sans-serif", "padding": "20px"})

# --- Callbacks ---
@app.callback(
    Output("mentions-line", "figure"),
    Output("sentiment-platform-bar", "figure"),
    Input("month-selector", "value"),
    Input("platform-selector", "value")
)
def update_graphs(selected_months, selected_platforms):
    filtered = data[data["Month"].isin(selected_months) & data["Platform"].isin(selected_platforms)]

    # Mentions over time
    line = px.line(
        filtered.groupby(["Month", "Sentiment"]).sum(numeric_only=True).reset_index(),
        x="Month", y="Mentions", color="Sentiment",
        markers=True,
        color_discrete_map={"Positive": "#d71f26", "Neutral": "#333333", "Negative": "#999999"}
    )

    # Sentiment by platform
    bar = px.bar(
        filtered.groupby(["Platform", "Sentiment"]).sum(numeric_only=True).reset_index(),
        x="Platform", y="Mentions", color="Sentiment", barmode="group",
        color_discrete_map={"Positive": "#d71f26", "Neutral": "#333333", "Negative": "#999999"}
    )

    return line, bar

server = app.server

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=10000)
