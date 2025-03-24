# Covid19-Interactive-Map
import folium
from folium.plugins import HeatMap, MarkerCluster, FeatureGroupSubGroup
import pandas as pd

# Load the COVID-19 dataset with latitude and longitude
file_path = "Covid19-Dataset-With-Coordinates.csv"
df = pd.read_csv(file_path)

# Create a dark-themed map
world_map = folium.Map(location=[20, 0], zoom_start=2, tiles="CartoDB dark_matter")

# Main marker cluster
main_cluster = MarkerCluster().add_to(world_map)

# Create sub-groups for filtering
confirmed_group = FeatureGroupSubGroup(main_cluster, "Confirmed Cases")
deaths_group = FeatureGroupSubGroup(main_cluster, "Deaths")
recovered_group = FeatureGroupSubGroup(main_cluster, "Recovered")

# Add sub-groups to the map
world_map.add_child(confirmed_group)
world_map.add_child(deaths_group)
world_map.add_child(recovered_group)

# Adding markers with hover effect
for _, row in df.iterrows():
    if not pd.isnull(row["Latitude"]) and not pd.isnull(row["Longitude"]):
        # Define hover text with detailed info
        hover_text = f"""
        <b>🇨🇴 {row['Country/Region']}</b><br>
        🔹 <b>Confirmed Cases:</b> {row['Confirmed']}<br>
        🔴 <b>Deaths:</b> {row['Deaths']}<br>
        🟢 <b>Recovered:</b> {row['Recovered']}
        """
        
        # Add markers to respective groups
        folium.CircleMarker(
            location=[row["Latitude"], row["Longitude"]],
            radius=max(row['Confirmed'] / 5000000, 5),
            color='blue',
            fill=True,
            fill_color='blue',
            fill_opacity=0.6,
            tooltip=folium.Tooltip(hover_text)  # Enhanced hover effect
        ).add_to(confirmed_group)

        folium.CircleMarker(
            location=[row["Latitude"], row["Longitude"]],
            radius=max(row['Deaths'] / 500000, 3),
            color='red',
            fill=True,
            fill_color='red',
            fill_opacity=0.6,
            tooltip=folium.Tooltip(hover_text)
        ).add_to(deaths_group)

        folium.CircleMarker(
            location=[row["Latitude"], row["Longitude"]],
            radius=max(row['Recovered'] / 5000000, 5),
            color='green',
            fill=True,
            fill_color='green',
            fill_opacity=0.6,
            tooltip=folium.Tooltip(hover_text)
        ).add_to(recovered_group)

# Add Layer Control to enable toggling
folium.LayerControl(collapsed=False).add_to(world_map)

# Save the final interactive map
world_map.save("Hover_Covid19_Map.html")

print("✅ Interactive COVID-19 Map Generated Successfully! Open Hover_Covid19_Map.html to view it.")
