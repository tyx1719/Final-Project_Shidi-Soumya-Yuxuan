# Final-Project_Shidi-Soumya-Yuxuan
ENV 872 Final Project

## Summary
This repository is designed for the course final project of ENV872. The group members are Shidi Dai, Soumya Mathew, and Yuxuan Tian. This study examines the number and types of on road motor vehicles that are most responsible for California's carbon monoxide and nitrogen oxide emissions over the course of 5 years (2018–2022). This repository contains folders including raw data, processed data and data analysis.  

## Investigators
Shidi Dai, Soumya Mathew, Yuxuan Tian

## Keywords
California, Air pollution, Vehicle, Carbon monoxide, Nitrogen oxide

## Database Information
This project uses data from two sources: California Air Resources Board and California Department of Motor Vehicles. Both datasets were retrieved on November 23, 2022.   

## Folder structure, file formats, and naming conventions 

The repository of this project contains one folder named "Data". In the Data folder, there are three subfolders: MetaData, Processed, and Raw. In the Raw folder, there are six datasets collected from two sources: California Air Resources Board and California Department of Motor Vehicles. All six datasets are in csv format. In the MetaData folder, there are pdf files from the data source websites describing the raw data sets we use. They are in pdf format. In the Processed folder, there are six datasets that the project group processed from the raw datasets. They are all in csv format.

The repository also contains a r markdown file: Code_Main markdown. This file contains all of the codes we use for this project. It is in rmd format.

The datasets in both Raw and Processed folders are csv files. The raw datasets are original data we collected from the sources. We wrangle these datasets to form new datasets with information needed for analyses. The processed datasets are what we wrangled from the raw datasets. We use them in the analysis section. The pdf in MetaData folder is for reference in understanding the raw datas.

The files are named based on the key words of information we want to use from the content. The dataset files' names are key words of what we will test. 

## Metadata

Vehicle Fuel Type Count by Zip Code
- 7 Variables: Date, Zip.Code, Model.Year, Fuel, Make, Duty, Vehicles
- Class: Factor
- Unit: Vehicle
- Year: 2018, 2020, 2021, 2022

Emission Projections by Carbon Monoxide
- 13 Variables: AREA, SRC_TYPE, CATEGORY, SUBCATEGORY, POLLUTANT, SEASON, CONTROL_TYPE, X2018, X2019, X2020, X2021, X2022, v1.03_RF3084
- Class: Factor
- Unit: Tons per Day
- Year: 2018, 2019, 2020, 2021, 2022

Emission Projections by Oxide of Nitrogen
- 13 Variables: AREA, SRC_TYPE, CATEGORY, SUBCATEGORY, POLLUTANT, SEASON, CONTROL_TYPE, X2018, X2019, X2020, X2021, X2022, v1.03_RF3084
- Class: Factor
- Unit: Tons per Day
- Year: 2018, 2019, 2020, 2021, 2022

## Scripts and code
#Technology used
  R
#Setup/Installation Requirements
- Clone this repository to your R studio
- Open "Dai,Mathew,Tian_ENV872_Project.Rmd"
- install.packages("tidyverse"/"lubridate"/"trend"/"zoo"/"reshape2"/"reshape")
  R
#Import dataset
Since the datasets locate in the repository, simply run the following line could extract the dataset concerned. 
 carbon <- read.csv("Data/Raw/Carbon_Monoxide.csv", stringsAsFactors = TRUE)
 
#Data wrangling
The team clean up the dataset and generate new datasets for further analysis. The original datasets pulled from the website are stored in the folder named "raw_data". The datasets generated are in the folder named "processed_data."

- Find and combine vehicle numbers from different year data set and categorize with different fuel
  Q2018$Vehicles <- as.numeric(Q2018$Vehicles)
  unique(Q2018$Fuel)
  sum(Q2018[which(Q2018$Fuel =="Gasoline"), 7])
  sum(Q2018[which(Q2018$Fuel =="Diesel and Diesel Hybrid"), 7])
  sum(Q2018[which(Q2018$Fuel =="Natural Gas"), 7])
  sum(Q2018[which(Q2018$Fuel =="Hybrid Gasoline"), 7])
  sum(Q2018[which(Q2018$Fuel =="Flex-Fuel"), 7])
  sum(Q2018[which(Q2018$Fuel =="Battery Electric"), 7])
  sum(Q2018[which(Q2018$Fuel =="Other"), 7])
  sum(Q2018[which(Q2018$Fuel =="Plug-in Hybrid"), 7])
  sum(Q2018[which(Q2018$Fuel =="Hydrogen Fuel Cell"), 7])
  
- Create the new dataframe
  VehicleNumber.Fuel <- data.frame(Year = c("2018", "2019", "2020", "2021", "2022"),
                                 Gasoline = c("26978798", "26978798", "26985319", "25253808","26313272"),
                                 Diesel_Diesel_Hybrid = c("1282387", "1282387", "1271338", "1199349", "1306851"),
                                 Natural_Gas = c("34982", "34982", "29096", "10881", "31403"),
                                 Hybrid_Gasoline = c("1079558", "1079558", "1123090", "1181387", "1299331"),
                                 Flex_Fuel = c("1318691", "1318691", "1257292", "1259789", "1246662"),
                                 Battery_Electric = c("226614", "226614", "306803", "374865", "526266"),
                                 Other = c("7824", "7824", "5867", "47847", "3801"),
                                 Plug_in_Hybrid = c("204002", "204002", "248388", "264112", "305526"),
                                 Hydrogen_Fuel_Cell = c("5138", "5138", "6648", "7481", "10129"))
- Select the data of interest
  Q2018.gasoline <- 
    Q2018 %>%
    filter(Fuel == "Gasoline") %>%
    select(Fuel, Duty, Vehicles)
  sum(Q2018.gasoline[which(Q2018.gasoline$Duty =="Light"), 3])
  sum(Q2018.gasoline[which(Q2018.gasoline$Duty =="Heavy"), 3])

- Create the master dataframe
  Master_table_vehicles <- rbind(GasolineVehicle.Duty_a,DieselVehicle.Duty_a,HybridGasVehicle.Duty_a,NaturalGasVehicle.Duty_a,FlexFuelVehicle.Duty_a) %>%
  mutate(Total_vehicle = as.numeric(Light) + as.numeric(Heavy) ) %>%
  mutate(Share_Light = Light/Total_vehicle) %>%
  mutate(Share_Heavy = Heavy/Total_vehicle)
  
- Change the variable types 
  COT.gas$`LIGHT DUTY PASSENGER (LDA)` <- as.numeric(COT.gas$`LIGHT DUTY PASSENGER (LDA)`)

#Analysis
- Trend analysis #1 
  - Number of cars changes over the years with plots
    - Only gasoline fueled
    - Not gas-fueled
    - Heavy and light duty cars over time
    
  First, a new dataset for the trend analysis is generated. Then, create the graph with the package "ggplot2."
  ds1 <- melt(VehicleNumber.Fuel,id="Year")
  ds1$value <- as.numeric(ds1$value)
  ds1_sub <- subset (ds1, variable != "Gasoline")
  ds1_sub2 <- subset (ds1, variable == "Gasoline")
  
  ggplot(ds1_sub, aes(x=Year,y=value,group=variable,color=variable))+
    geom_point()+
    geom_line()+
    xlab("Year")+
    ylab("Quantity")+
    theme(legend.position = "right")+
    labs(color="Type of vehicles")

- Trend analysis #2
  - COT
    - Diesel/gas
    - Heavy/light
  - NOX
    - Diesel/gas
    - Heavy/light
    
  Generate a new dataset, then plot the data.
  ds2_d <- melt(NOX.diesel,id="Year")
  ds2_d$value <- as.numeric(ds2_d$value)
  ggplot(ds2_d, aes(x=Year,y=value,group=variable,color=variable))+
    geom_point()+
    geom_line()+
    xlab("Year")+
    ylab("Emission")+
    theme(legend.position = "right",legend.key.size = unit(.5, 'cm'))+
    labs(color="Type of vehicles")

- Regression
  - Simple regression
    - COT
      - Diesel/gas
    -NOX
      - Diesel/gas
  reg1 <- lm(data = ds4, Emission.diesel ~ Quantity.diesel)
  summary(reg1)
  
  - Multivariate regression
  reg6 <- lm(data = ds6, 
           Total_Emission ~  `HEAVY DUTY DIESEL URBAN BUSES (UBD)` + `HEAVY DUTY GAS URBAN BUSES (UBG)` )
  summary(reg6)
 
**This project followed ENV 872 class practices and weekly assignments.** 

## Quality assurance/quality control

<https://www.dataone.org/best-practices/develop-quality-assurance-and-quality-control-plan>
<https://www.dataone.org/best-practices/ensure-basic-quality-control>
<https://www.dataone.org/best-practices/communicate-data-quality>
<https://www.dataone.org/best-practices/identify-outliers>
<https://www.dataone.org/best-practices/identify-values-are-estimated>