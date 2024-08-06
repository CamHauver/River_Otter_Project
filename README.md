# River_Otter_Project
Data analysis of trail-cam species sightings as part of a conservation research project. Data is transformed/anonymized and originally formatted in Excel. Data cleaning, analysis, and visualizations performed using Pandas and MatPlotLib.

Objectives:
1) Reformat/clean data
2) Perform exploratory analysis
Answer these questions:
    1) How do animal sightings differ between survey locations?
    2) Which individual surveys within each location had the most/least animal sightings?
    3) Where were river otters sighted, and how often were they sighted compared to other animals?
    4)Compare probabilities of other animal sightings to river otter sightings. Are there any possible patterns?

Description of data:
Data was originally provided in an excel workbook across five worksheets with inconsitent data entry
Data was manually re-entered/compiled into a single excel spreadsheet with 6 columns (Location, Survey #, Camera #, SD Card, Animal Sighted, and Animal Count) for further cleaning) and 310 rows
Data was altered to protect proprietary information. Data published on this repo is theorhetical, not the actual data collected for the conservation research project

Executable Script from Jupyter Notebook:

# Import Dependencies
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import scipy.stats as st
from scipy.stats import chi2_contingency


# In[396]:


#Import data
otter_data_csv = "./Resources/example_data/altered_otter_data_compiled.csv"


# In[397]:


#Read data
otter_data = pd.read_csv(otter_data_csv)


# In[398]:


#Convert data to DataFrame
otter_data_df = pd.DataFrame(otter_data)


# In[399]:


#Check Data types
otter_data_df.dtypes


# In[400]:


#Check columns
list(otter_data_df.columns)


# In[401]:


#Get a list of all animals sighted
animal_sighted_list = otter_data_df["Animal Sighted"].unique()
animal_sighted_list

animal_sighted_list_sorted = sorted(animal_sighted_list)
animal_sighted_list_sorted


# In[402]:


#Clean misspelled or close-duplicate Animals Sighted. Make a new cleaned dataframe
cleaned_otter_data_df = otter_data_df.replace(
    {"Common Merghansar": "Common Merghanser", 
     "Cat": "Domestic Cat", "House Cat": "Domestic Cat", 
     "Hawk": "Hawk (unidentified)",
     "Mallard Ducks": "Mallard Duck",
     "Pelicans": "Pelican",
     "Rodent (unknown species)": "Unidentified Rodent", "Rodent (unidentified)": "Unidentified Rodent",
     "Swallows": "Swallow",
     "Unidentified bird": "Unidentified Bird", "Unknown Bird": "Unidentified Bird",
     "Unknown Mammal": "Unidentified Mammal",
     "Fish (unidentified)": "Unidentified Fish",  
})
cleaned_otter_data_df


# In[403]:


#Get rid of any duplicates in cleaned dataframe
cleaned_otter_data_df[cleaned_otter_data_df.duplicated(keep=False)]
cleaned_otter_data_df


# In[688]:


#Get number of unique animals sighted (total number of species sighted)
cleaned_animal_sighted_list = cleaned_otter_data_df["Animal Sighted"].unique()
cleaned_animal_sighted_list

cleaned_animal_sighted_list_sorted = sorted(cleaned_animal_sighted_list)
cleaned_animal_sighted_list_sorted_count = len(cleaned_animal_sighted_list_sorted)
cleaned_animal_sighted_list_sorted_count


# In[405]:


#Count up how many times each species was sighted, overall
animal_sighted_totals_df = pd.DataFrame(cleaned_otter_data_df["Animal Sighted"].value_counts())
animal_sighted_totals_df.columns =["Count"]
animal_sighted_totals_df


# In[406]:


#Get the total number of animal sightings, overall
total_animal_sightings_count = cleaned_otter_data_df["Animal Sighted"].count()
total_animal_sightings_count


# In[407]:


#Graph total animal sightings for each species from all surveys
animal_sighted_totals_df_graph = animal_sighted_totals_df.plot(kind="bar", facecolor="lightblue", figsize=(20,4))
plt.title("Total Animal Sightings (All Surveys)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")


# In[408]:


#Graph top animal sightings from all surveys
top_animal_sighted_totals_df_graph = animal_sighted_totals_df.head(19).plot(kind="bar", facecolor="lightblue", figsize=(10,4))
plt.title("Top Animal Sightings (All Surveys)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")


# In[419]:


#Create a new dataframe of Lower Provo River data
lower_provo_river_df = cleaned_otter_data_df[cleaned_otter_data_df["Location"] == "Lower Provo River"]
lower_provo_river_df


# In[599]:


#Get a count of animals sightings for each individual survey within the Lower Provo River
lpr_survey_number_totals_df = pd.DataFrame(lower_provo_river_df["Survey #"].value_counts())
lpr_survey_number_totals_df.columns =["LPR Count"]
lpr_survey_number_totals_df


# In[620]:


#Sort Lower Prover River survey animal sighting counts from lowest survey # to highest
lpr_survey_number_totals_df_sorted = lpr_survey_number_totals_df.sort_values(["Survey #"], ascending=True)
lpr_survey_number_totals_df_sorted


# In[623]:


#Get summary stats of Lower Prover River survey animal sighting counts
lpr_survey_number_totals_df_sorted.describe()


# In[621]:


#Create graph of animal sightings per survey # in the Lower Provo River
lpr_survey_number_totals_df_sorted_graph = lpr_survey_number_totals_df_sorted.plot(kind="bar", facecolor="darkblue", figsize=(6,4))
plt.title("Animal Sightings per Survey (Lower Provo River)")
plt.xlabel("Survey Number")
plt.xticks(rotation=0)
plt.ylabel("Number of Sightings")
plt.ylim(0,20)


# In[463]:


#Create a dataframe of the number of animal sightings per species sighted in the Lower Provo River
lpr_animal_sighted_totals_df = pd.DataFrame(lower_provo_river_df["Animal Sighted"].value_counts())
lpr_animal_sighted_totals_df.columns =["LPR Count"]
lpr_animal_sighted_totals_df


# In[464]:


#Get a count of the total number of animal sightings in the Lower Provo River
lpr_total_animal_sightings_count = lower_provo_river_df["Animal Sighted"].count()
lpr_total_animal_sightings_count


# In[682]:


#Get a count of the number of different species sighted in the Lower Provo River
lpr_animal_sighted_list = lower_provo_river_df["Animal Sighted"].unique()
lpr_animal_sighted_list_count = len(lpr_animal_sighted_list)
lpr_animal_sighted_list_count


# In[689]:


#Graph the total animal sightings for each species in the Lower Provo River
lpr_animal_sighted_totals_df_graph = lpr_animal_sighted_totals_df.plot(kind="bar", facecolor="darkblue", figsize=(10,4))
plt.title("Total Animal Sightings (Lower Provo River)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")
plt.ylim(0,25)


# In[466]:


#Get a list of the Lower Provo River survey #s
lpr_survey_number_list = lower_provo_river_df["Survey #"].unique()

lpr_survey_number_list_sorted = sorted(lpr_survey_number_list)
lpr_survey_number_list_sorted


# In[467]:


#Create a dataframe with animal sighting totals for each Survey # in the Lower Provo River
lpr_survey_number_totals_df = pd.DataFrame(lower_provo_river_df["Survey #"].value_counts())
lpr_survey_number_totals_df.columns =["Animal Sightings Count"]
lpr_survey_number_totals_df


# In[468]:


#Create a dataframe of Middle Provo River data only
middle_provo_river_df = cleaned_otter_data_df[cleaned_otter_data_df["Location"] == "Middle Provo River"]
middle_provo_river_df


# In[600]:


#Create a dataframe of number of animal sightings per each Survey # in the Middle Provo River
mpr_survey_number_totals_df = pd.DataFrame(middle_provo_river_df["Survey #"].value_counts())
mpr_survey_number_totals_df.columns =["MPR Count"]
mpr_survey_number_totals_df


# In[617]:


#Sort Middle Provo River survey animal sighting counts by Survey # 
mpr_survey_number_totals_df_sorted = mpr_survey_number_totals_df.sort_values(["Survey #"], ascending=True)
mpr_survey_number_totals_df_sorted


# In[624]:


#Get summary stats of Middle Provo River survey animal sighting counts
mpr_survey_number_totals_df_sorted.describe()


# In[619]:


#Create graph of number of animal sightings per survey in the Middle Provo River
mpr_survey_number_totals_df_sorted_graph = mpr_survey_number_totals_df_sorted.plot(kind="bar", facecolor="orange", figsize=(10,4))
plt.title("Animal Sightings per Survey (Middle Provo River)")
plt.xlabel("Survey Number")
plt.xticks(rotation=0)
plt.ylabel("Number of Sightings")
plt.ylim(0,20)


# In[469]:


#Create a dataframe of the total number of animal sightings per species sighted at the Middle Provo River
mpr_animal_sighted_totals_df = pd.DataFrame(middle_provo_river_df["Animal Sighted"].value_counts())
mpr_animal_sighted_totals_df.columns =["MPR Count"]
mpr_animal_sighted_totals_df


# In[470]:


#Get a count of the total animal sightings in the Middle Provo River
mpr_total_animal_sightings_count = middle_provo_river_df["Animal Sighted"].count()
mpr_total_animal_sightings_count


# In[683]:


#Get a count of the number of different survey #s for the Middle Provo River
mpr_survey_number_list = middle_provo_river_df["Survey #"].unique()

mpr_survey_number_list_sorted = sorted(mpr_survey_number_list)
mpr_survey_number_list_sorted
mpr_survey_number_list_sorted_count = len(mpr_survey_number_list_sorted)
mpr_survey_number_list_sorted_count


# In[681]:


#Get a count of the total number of different species sighted at the Middle Provo River
mpr_animal_sighted_list = middle_provo_river_df["Animal Sighted"].unique()
mpr_animal_sighted_list_count = len(mpr_animal_sighted_list)
mpr_animal_sighted_list_count


# In[472]:


#
mpr_animal_sighted_totals = middle_provo_river_df["Animal Sighted"].value_counts()
mpr_animal_sighted_totals


# In[595]:


#Graph the total number of animal sightings per species sighted at the Middle Provo River
mpr_animal_sighted_totals_graph = mpr_animal_sighted_totals.plot(kind="bar", facecolor="orange", figsize=(15,4))
plt.title("Total Animal Sightings (Middle Provo River)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")
plt.ylim(0,25)


# In[474]:


#Create a dataframe of Strawberry River data
strawberry_river_df = cleaned_otter_data_df[cleaned_otter_data_df["Location"] == "Strawberry River"]
strawberry_river_df


# In[601]:


#Create a dataframe of the number of animal sightings for each Survey # for the Strawberry River
sr_survey_number_totals_df = pd.DataFrame(strawberry_river_df["Survey #"].value_counts())
sr_survey_number_totals_df.columns =["SR Count"]
sr_survey_number_totals_df


# In[615]:


#Sort the survey animal sighting counts by ascending Survey # for the Strawberry River
sr_survey_number_totals_df_sorted = sr_survey_number_totals_df.sort_values(["Survey #"], ascending=True)
sr_survey_number_totals_df_sorted


# In[625]:


#Get summary statistics of Strawberry River survey animal sighting counts
sr_survey_number_totals_df_sorted.describe()


# In[616]:


#Graph Strawberry River animal sighting counts per Survey # 
sr_survey_number_totals_df_sorted_graph = sr_survey_number_totals_df_sorted.plot(kind="bar", facecolor="green", figsize=(6,4))
plt.title("Animal Sightings per Survey (Strawberry River)")
plt.xlabel("Survey Number")
plt.xticks(rotation=0)
plt.ylabel("Number of Sightings")
plt.ylim(0,20)


# In[475]:


#Create a dataframe of the total number of animal sightings for each species sighted at the Strawberry River
sr_animal_sighted_totals_df = pd.DataFrame(strawberry_river_df["Animal Sighted"].value_counts())
sr_animal_sighted_totals_df.columns =["SR Count"]
sr_animal_sighted_totals_df


# In[476]:


#Get a count of the total number of animal sightings at the Strawberry River
sr_total_animal_sightings_count = strawberry_river_df["Animal Sighted"].count()
sr_total_animal_sightings_count


# In[685]:


#Get a count of the number of different animal species sighted at the Strawberry River
sr_animal_sighted_list = strawberry_river_df["Animal Sighted"].unique()
sr_animal_sighted_list_count = len(sr_animal_sighted_list)
sr_animal_sighted_list_count


# In[477]:


#Get a list of different survey #s at the Strawberry River
sr_survey_number_list = strawberry_river_df["Survey #"].unique()

sr_survey_number_list_sorted = sorted(sr_survey_number_list)
sr_survey_number_list_sorted


# In[478]:


#Get counts of animal sightings per species sighted at the Strawberry River
sr_animal_sighted_totals = strawberry_river_df["Animal Sighted"].value_counts()
sr_animal_sighted_totals


# In[594]:


#Graph total animal sightings for each species sighted at the Strawberry River
sr_animal_sighted_totals_graph = sr_animal_sighted_totals.plot(kind="bar", facecolor="green", figsize=(12,4))
plt.title("Total Animal Sightings (Strawberry River)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")
plt.ylim(0,25)


# In[480]:


#Create a dataframe of Weber River Data
weber_river_df = cleaned_otter_data_df[cleaned_otter_data_df["Location"] == "Weber River"]
weber_river_df


# In[602]:


#Get animal sighting counts for each survey # at the Weber River
wr_survey_number_totals_df = pd.DataFrame(weber_river_df["Survey #"].value_counts())
wr_survey_number_totals_df.columns =["WR Count"]
wr_survey_number_totals_df


# In[610]:


#Sort survey animal sighted counts by ascending Survey #
wr_survey_number_totals_df_sorted = wr_survey_number_totals_df.sort_values(["Survey #"], ascending=True)
wr_survey_number_totals_df_sorted


# In[626]:


#Get summary stats of Weber River survey animal sighted counts
wr_survey_number_totals_df_sorted.describe()


# In[614]:


#Graph survey animal sighted counts per survey # at the Weber River
wr_survey_number_totals_df_sorted_graph = wr_survey_number_totals_df_sorted.plot(kind="bar", facecolor="red", figsize=(6,4))
plt.title("Animal Sightings per Survey (Weber River)")
plt.xlabel("Survey Number")
plt.xticks(rotation=0)
plt.ylabel("Number of Sightings")
plt.ylim(0,20)


# In[482]:


#Create a dataframe of number of animal sightings for each species sighted at the Weber River
wr_animal_sighted_totals_df = pd.DataFrame(weber_river_df["Animal Sighted"].value_counts())
wr_animal_sighted_totals_df.columns =["WRCount"]
wr_animal_sighted_totals_df


# In[483]:


#Get a count of the total number of animal sightings at the Weber River
wr_total_animal_sightings_count = weber_river_df["Animal Sighted"].count()
wr_total_animal_sightings_count


# In[684]:


#Get a count of the number of different species sighted at the Weber River
wr_animal_sighted_list = weber_river_df["Animal Sighted"].unique()
wr_animal_sighted_list_count = len(wr_animal_sighted_list)
wr_animal_sighted_list_count


# In[484]:


#Get a list of Survey #s at the Weber River
wr_survey_number_list = weber_river_df["Survey #"].unique()

wr_survey_number_list_sorted = sorted(wr_survey_number_list)
wr_survey_number_list_sorted


# In[485]:


#Get totals of total number of animal sightings for each species sighted at the Weber River
wr_animal_sighted_totals = weber_river_df["Animal Sighted"].value_counts()
wr_animal_sighted_totals


# In[486]:


#Graph the total number of animal sightings for each species sighted at the Weber River
wr_animal_sighted_totals_graph = wr_animal_sighted_totals.plot(kind="bar", facecolor="red", figsize=(6,4))
plt.title("Total Animal Sightings (Weber River)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")
plt.ylim(0,25)


# In[494]:


#Merge dataframes of animal sighting totals for each Location into one DataFrame
lpr_mpr_animal_sighted_totals = pd.merge(lpr_animal_sighted_totals_df, mpr_animal_sighted_totals_df, on="Animal Sighted", how="outer")
lpr_mpr_sr_animal_sighted_totals = pd.merge(lpr_mpr_animal_sighted_totals, sr_animal_sighted_totals_df, on="Animal Sighted", how="outer")
lpr_mpr_sr_wr_animal_sighted_totals = pd.merge(lpr_mpr_sr_animal_sighted_totals, wr_animal_sighted_totals_df, on="Animal Sighted", how="outer")
lpr_mpr_sr_wr_animal_sighted_totals


# In[492]:


#Fill NaNs in merged DataFrame of animal sighting total counts w/ 0
lpr_mpr_sr_wr_animal_sighted_totals_filled = lpr_mpr_sr_wr_animal_sighted_totals.fillna(0)
lpr_mpr_sr_wr_animal_sighted_totals_filled


# In[520]:


#Get summary statistics of animal sighting total counts for all locations
lpr_mpr_sr_wr_animal_sighted_totals_filled.describe()


# In[691]:


#Sort animal sighting total counts for each location Dataframe by animal species most frequently sighted, descending
lpr_mpr_sr_wr_animal_sighted_totals_filled_sorted = lpr_mpr_sr_wr_animal_sighted_totals_filled.sort_values(["MPR Count", "LPR Count", "SR Count", "WR Count"], ascending=False)
lpr_mpr_sr_wr_animal_sighted_totals_filled_sorted


# In[692]:


#Graph animal sighted totals for all locations on one Graph
all_location_animal_sighted_totals_graph = lpr_mpr_sr_wr_animal_sighted_totals_filled_sorted.plot(kind="bar", figsize=(20,4))
all_location_animal_sighted_totals_graph
plt.title("Animal Sightings (All Locations)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")
plt.ylim(0,25)


# In[693]:


#Identify sites w/ Otter sightings

otter_sites_df = cleaned_otter_data_df[cleaned_otter_data_df["Animal Sighted"] == "Otter"]
otter_sites_df


# In[503]:


#Create new dataframe with all data from Otter Sites only

animal_sighted_survey_5_df = middle_provo_river_df[middle_provo_river_df["Survey #"] == 5][middle_provo_river_df["Animal Sighted"] != "Otter"]
animal_sighted_survey_13_df = middle_provo_river_df[middle_provo_river_df["Survey #"] == 13][middle_provo_river_df["Animal Sighted"] != "Otter"]
animal_sighted_survey_15_df = middle_provo_river_df[middle_provo_river_df["Survey #"] == 15][middle_provo_river_df["Animal Sighted"] != "Otter"]
animal_sighted_survey_17_df = middle_provo_river_df[middle_provo_river_df["Survey #"] == 17][middle_provo_river_df["Animal Sighted"] != "Otter"]
animal_sighted_survey_18_df = middle_provo_river_df[middle_provo_river_df["Survey #"] == 18][middle_provo_river_df["Animal Sighted"] != "Otter"]
animal_sighted_survey_32_df = middle_provo_river_df[middle_provo_river_df["Survey #"] == 32][middle_provo_river_df["Animal Sighted"] != "Otter"]



animal_sighted_otter_survey_list = [
    animal_sighted_survey_5_df,
    animal_sighted_survey_13_df,
    animal_sighted_survey_15_df,
    animal_sighted_survey_17_df,
    animal_sighted_survey_18_df,
    animal_sighted_survey_32_df
]

merged_otter_site_df = pd.concat(animal_sighted_otter_survey_list)

merged_otter_site_df


# In[504]:


#Create a new dataframe of total number of animal sightings for each species sighted at Otter Sites
os_animal_sighted_totals_df = pd.DataFrame(merged_otter_site_df["Animal Sighted"].value_counts())
os_animal_sighted_totals_df.columns =["Count"]
os_animal_sighted_totals_df


# In[505]:


#Get a count of the total number of animal sightings at Otter Sites
os_total_animal_sightings_count = merged_otter_site_df["Animal Sighted"].count()
os_total_animal_sightings_count


# In[506]:


# Create a new column for sighting probability for each animal sighted at otter sites:
os_animal_sighted_totals_df["Otter Sites"] = os_animal_sighted_totals_df["Count"] / os_total_animal_sightings_count
os_sighting_probabilities = os_animal_sighted_totals_df.drop(["Count"], axis=1)
os_sighting_probabilities


# In[507]:


#Get total counts of animal sightings for each animal species sighted at Otter Sites
otter_site_animal_sighted_totals = merged_otter_site_df["Animal Sighted"].value_counts()
otter_site_animal_sighted_totals


# In[573]:


#Create a DataFrame of total number of animal sightings for each animal species sighted at Otter Sites
otter_sites_animal_sighted_totals_df = pd.DataFrame(otter_site_animal_sighted_totals)
otter_sites_animal_sighted_totals_df = otter_sites_animal_sighted_totals_df.rename(columns={"count": "Otter Site"})
otter_sites_animal_sighted_totals_df


# In[593]:


#Graph the total number of animal sightings for each species sighted at Otter Sites
otter_site_animal_sighted_totals_graph = otter_site_animal_sighted_totals.plot(kind="bar", facecolor="darkblue", figsize=(10,6))
plt.title("Total Animal Sightings (Otter Sites Only)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")
plt.ylim(0,25)


# In[636]:


#Identify sites w/out Otter sightings in the middle prover river.

mpr_non_otter_sites_df =middle_provo_river_df[middle_provo_river_df["Survey #"] != 5][middle_provo_river_df["Survey #"] != 13][middle_provo_river_df["Survey #"] != 15][middle_provo_river_df["Survey #"] != 17][middle_provo_river_df["Survey #"] != 18][middle_provo_river_df["Survey #"] != 32]
mpr_non_otter_sites_df


# In[640]:


#Create a DataFrame of total number of animal sightings of each species sighted at Non-Otter Sites at the Middle Provo River
non_os_animal_sighted_totals_df = pd.DataFrame(mpr_non_otter_sites_df["Animal Sighted"].value_counts())
non_os_animal_sighted_totals_df.columns =["MPR Non-Otter Site"]
non_os_animal_sighted_totals_df


# In[641]:


#Get counts of the total number of animal sightings of each species sighted at Non-Otter sites at the Middle Provo River
mpr_non_otter_sites_animal_sighted_totals = mpr_non_otter_sites_df["Animal Sighted"].value_counts()
mpr_non_otter_sites_animal_sighted_totals


# In[643]:


#Graph the total number of animal sightings of each species sighted at Non-Otter sites at the Middle Provo River
mpr_non_otter_sites_animal_sighted_totals_graph = mpr_non_otter_sites_animal_sighted_totals.plot(kind="bar", facecolor="orange", figsize=(10,6))
plt.title("Total Animal Sightings (MPR Non-Otter Sites Only)")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")
plt.ylim(0,25)


# In[648]:


#Merge the Otter Site animal sighting dataframe and Middle Provo River Non-Otter Site animal sighting dataframe
os_mpr_non_os_animal_sighted_totals = pd.merge(otter_sites_animal_sighted_totals_df, non_os_animal_sighted_totals_df, on="Animal Sighted", how="outer")
os_mpr_non_os_animal_sighted_totals


# In[649]:


#Replace NaN values in new merged Otter Site/Non-Otter Site dataframe with 0s
os_mpr_non_os_animal_sighted_totals_filled = os_mpr_non_os_animal_sighted_totals.fillna(0)
os_mpr_non_os_animal_sighted_totals_filled


# In[651]:


#Sort values in Otter Site/Non-Otter Site dataframe descending from most frequently sighted animal species down in the Otter Site column
os_mpr_non_os_animal_sighted_totals_filled_sorted = os_mpr_non_os_animal_sighted_totals_filled.sort_values(["Otter Site", "MPR Non-Otter Site"], ascending=False)
os_mpr_non_os_animal_sighted_totals_filled_sorted


# In[652]:


#Get summary statistics on Otter Site/Non-Otter Site dataframe
os_mpr_non_os_animal_sighted_totals_filled_sorted.describe()


# In[698]:


#Graph the number of animal sightings for each species at both Otter Sites and Middle Provo River Non-Otter sites with different colored bar graphs for Otter Sites and Non-Otter Sites
os_mpr_non_os_animal_sighted_totals_filled_sorted_graph = os_mpr_non_os_animal_sighted_totals_filled_sorted.plot(kind="bar", figsize=(20,4))
os_mpr_non_os_animal_sighted_totals_filled_sorted
plt.title("Middle Provo River Animal Sightings at Otter Sites vs Non-Otter Sites")
plt.xlabel("Animals Sighted")
plt.ylabel("Number of Sightings")
plt.ylim(0,20)


# In[699]:


#Get the total number of animal sightings at Non-Otter sites on the Middle Provo River 
non_os_total_animal_sightings_count = mpr_non_otter_sites_df["Animal Sighted"].count()
non_os_total_animal_sightings_count


# In[700]:


# Create a new column for sighting probability for each animal sighted at non-otter sites:
non_os_animal_sighted_totals_df["Non-Otter Sites"] = non_os_animal_sighted_totals_df["MPR Non-Otter Site"] / non_os_total_animal_sightings_count
non_os_sighting_probabilities = non_os_animal_sighted_totals_df.drop(["MPR Non-Otter Site"], axis=1)
non_os_sighting_probabilities


# In[702]:


#Merge dataframes to compile Otter Site animal sighting probabilities and Non-Otter site animal sighting probabilities
os_non_os_sighting_probabilities = pd.merge(os_sighting_probabilities, non_os_sighting_probabilities, on="Animal Sighted", how="outer")
os_non_os_sighting_probabilities


# In[703]:


#Fill NaN values in new probability dataframe w/ 0s
os_non_os_sighting_probabilities_filled = os_non_os_sighting_probabilities.fillna(0)
os_non_os_sighting_probabilities_filled


# In[704]:


#Sort Otter Site/Non-Otter Site probabilities in descending order
os_non_os_sighting_probabilities_filled_sorted = os_non_os_sighting_probabilities_filled.sort_values(["Otter Sites", "Non-Otter Sites"], ascending=False)
os_non_os_sighting_probabilities_filled_sorted


# In[705]:


#Plot animal sighting probabilities for each species sighted at Otter Sites and Non-Otter Sites on the Middle Provo River
sns.heatmap(os_non_os_sighting_probabilities_filled_sorted, annot=False, cbar_kws={"label": "Sighting Probability"}, yticklabels=True)
plt.yticks(fontsize = 8)
plt.xticks(rotation=45)
plt.title("Animal Sighting Probability at Otter Sites vs Non-Otter Sites")