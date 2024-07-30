# Patients Emergency Room Visit Power BI Dashboard

It shows the metrices created with the help of Patients Emergency Room Visit Report data. 
It has different sections that elaborates about the parameters of the total patient.

![image](https://github.com/user-attachments/assets/6ab189c6-1a6c-433a-9418-d27da04f6a79)


Following is the key metrices that are represented in the dashboard:

#### Total Patient Visits:
This metric represents the total number of patient visits within a given time period. It includes all types of visits, whether they are for administrative or non-administrative purposes.

#### Total Administrative Appointments:
This counts the number of appointments related to administrative purposes, such as scheduling, billing inquiries, or other non-medical consultations.

#### Total Non-Administrative Appointments:
This metric covers all appointments related to medical consultations, treatments, and any other healthcare services provided by the facility that are not administrative in nature.

#### Average Satisfaction:
This metric measures the average satisfaction level of patients based on feedback or surveys. It provides insights into the overall patient experience and quality of care received.

#### Service Not Rated:
This represents the number of services or interactions that were not rated by patients. It indicates gaps in feedback collection and areas where patient experiences are not being fully captured.

#### Average Wait Time:
This metric tracks the average amount of time patients wait before being attended to. It is a critical indicator of efficiency and can impact patient satisfaction.

#### Referred Patients vs. Walk-in Patients:
This metric compares the number of patients who were referred to the facility versus those who walked in without prior appointments. It helps in understanding patient referral patterns and the demand for walk-in services.
	
Following are the charts that are represented  in the dashboard:

#### Total Patients Visits: 
Total number of patients over the period of the time.

    Value Max_Min Line (Month) = 
    VAR _PatientTable = 
    CALCULATETABLE(
        ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Month]),
            "@TotalPatients", [Total Patients]
            ),
            ALLSELECTED()
        )

    VAR Min_Value = MINX(_PatientTable,[@TotalPatients])
    VAR Max_Value = MAXX(_PatientTable, [@TotalPatients])
    VAR Total_Patients = [Total Patients]

    RETURN

    SWITCH(
    TRUE(),
    Total_Patients = Min_Value, [Total Patients],
    Total_Patients = Max_Value, [Total Patients]
    )

![image](https://github.com/user-attachments/assets/7802826f-38d9-44f6-88b5-7c3c5b1922fc)

#### Administrative and Non- Administrative Appointments

![image](https://github.com/user-attachments/assets/69666976-8f75-4625-8b33-2a828d2829cd)   

    % Administrative Schedule = 
     DIVIDE(
        COUNTROWS(
            FILTER(
                'Patient Dataset',
                'Patient Dataset'[patient_admin_flag] = TRUE()
            )
        ), [Total Patients]
 )

    % Non - Administrative Schedule = 
    DIVIDE(
        COUNTROWS(
            FILTER(
                'Patient Dataset',
                'Patient Dataset'[patient_admin_flag] = FALSE()
            )
        ), [Total Patients]
     )


#### Total Patients By Departmental Store: 
This metric represents the total number of patients categorized by the specific departments they visited within a healthcare facility.

    Total Patients = COUNTROWS('Patient Dataset')   

![image](https://github.com/user-attachments/assets/f4c147af-04cb-4283-b35e-63c078b116b9)

#### Total Patient Visits Per Year:
This metric represents the total number of visits made by patients to a healthcare facility within a year.

    Value Max_Min Line (Month) = 
     VAR _PatientTable = 
     CALCULATETABLE(
        ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Month]),
            "@TotalPatients", [Total Patients]
            ),
            ALLSELECTED()
        ) 

    VAR Min_Value = MINX(_PatientTable,[@TotalPatients])
    VAR Max_Value = MAXX(_PatientTable, [@TotalPatients])
    VAR Total_Patients = [Total Patients]

    RETURN

    SWITCH(
    TRUE(),
    Total_Patients = Min_Value, [Total Patients],
    Total_Patients = Max_Value, [Total Patients]
    )


![image](https://github.com/user-attachments/assets/8dfdf349-7b3b-42e6-9415-81ef11535e82)

#### Average Satisfaction Score and Average wait time vs Patient Race:
This metric measures the average satisfaction scores of patients categorized by their race.

        Age Bucket = 
    SWITCH(
    TRUE(),
    'Patient Dataset'[patient_age] <=10, "0-10",
    'Patient Dataset'[patient_age] <=20, "11-20",
    'Patient Dataset'[patient_age] <=30, "21-30",
    'Patient Dataset'[patient_age] <=40, "31-40",
    'Patient Dataset'[patient_age] <=50, "41-50",
    'Patient Dataset'[patient_age] <=60, "51-60",
    'Patient Dataset'[patient_age] <=70, "61-70", "70+"
    )

        Avg SAT Score/ Wait Time Parameter = {
    ("Avg Satisfaction Score", NAMEOF('Calculations Table'[Average Satisfaction Score]), 1),
    ("Avg Wait Time", NAMEOF('Calculations Table'[Average Wait Time]), 0)
}

![image](https://github.com/user-attachments/assets/090e8c9c-e4b0-4456-bf50-f5b600b99b1d)

#### Total Patients by Week Type:
This metric categorizes the total number of patient visits based on different types of weeks. 

![image](https://github.com/user-attachments/assets/129af779-ad3f-47ec-9406-0abb44909b3a)

#### Total Patient by Age Group:
This metric represents the total number of patient visits categorized by different age groups. Common age groups might include infancy, early childhood, Teenager, middle childhood, and adult.

![image](https://github.com/user-attachments/assets/3d69b887-cafe-4a43-b236-b92a169081c0)

#### Average Satisfaction Score, Service Not Rated, Average wait time

![image](https://github.com/user-attachments/assets/e480d29f-cb63-4d1f-87da-9d6e792ca88f)

    Average Satisfaction Score = 
    CALCULATE(
            AVERAGE('Patient Dataset'[patient_sat_score]),
           'Patient Dataset'[patient_sat_score] <> BLANK()
     )



     % No Rating = 
    VAR _NoRating = CALCULATE(
                [Total Patients],
                'Patient Dataset'[patient_sat_score] = BLANK()
    )           
    RETURN
    DIVIDE(_NoRating, [Total Patients],0)


           
     Average Wait Time = 
     AVERAGE('Patient Dataset'[patient_waittime])

#### Gender Breakdown

![image](https://github.com/user-attachments/assets/7e50a02d-78ae-4aa1-8aaa-c45e0d7f0629)

      % _Male Visit = 
     VAR _MaleVisit = CALCULATE(
                [Total Patients],
                'Patient Dataset'[patient_gender]  = "M"
    ) 
    
    RETURN
    DIVIDE(_MaleVisit, [Total Patients],0)


        % Female Visit = 
        VAR _FemaleVisit = CALCULATE(
                [Total Patients],
                'Patient Dataset'[patient_gender]  = "F"
    )           

     RETURN
     DIVIDE(_MaleVisit, [Total Patients],0)


        % Unknown Visit = 
        VAR _MaleVisit = CALCULATE(
                [Total Patients],
                'Patient Dataset'[patient_gender] = "NC"
    )           

    RETURN
    DIVIDE(_MaleVisit, [Total Patients],0)

