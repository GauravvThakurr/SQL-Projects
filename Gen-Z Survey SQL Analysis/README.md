# Gen-Z SQL Survey Analysis

## Schema 

<img width="657" alt="Screenshot 2023-09-09 125557" src="https://github.com/GauravvThakurr/SQL-projects/assets/141028751/b39fd30e-ae5a-4a3a-bedb-6193df07d895">

***
## Power BI Dashboard 

<img width="997" alt="Screenshot 2023-11-19 175230" src="https://github.com/GauravvThakurr/Power-BI-Projects/assets/141028751/77b9eb7e-d622-42ae-82b4-e41b569e1a40">

<img width="995" alt="Screenshot 2023-11-19 175244" src="https://github.com/GauravvThakurr/Power-BI-Projects/assets/141028751/43ce1fb1-33be-474f-ab3a-af1417d281ac">

<img width="1010" alt="Screenshot 2023-11-19 175305" src="https://github.com/GauravvThakurr/Power-BI-Projects/assets/141028751/9f300b15-19c8-416a-bc80-b584f78fabf6">

CODE

```SQL
-- Question 1: How many Male have responded to the survey from India

SELECT COUNT(*) AS male_count
FROM personalized_info
WHERE CurrentCountry = 'India'
	AND Gender LIKE 'Male%'

-- Question 2: How many Female have responded to the survey from India

SELECT COUNT(*) AS Female_count
FROM personalized_info
WHERE CurrentCountry = 'India'
	AND Gender LIKE 'Female%' 

-- Question 3: How many of the Gen-Z are influenced by their parents in regards to their career choices from India

SELECT COUNT(*) AS "Influenced by Parents"
FROM learning_aspirations l
INNER JOIN personalized_info p ON p.ResponseID = l.ResponseID
WHERE l.CareerInfluenceFactor = 'My Parents'
	AND p.CurrentCountry = 'India' show keys
FROM learning_aspirations
WHERE key_name = 'Primary' 

-- Question 4: How many of the Female Gen-Z are influenced by their parents in regards to their career choices from India

SELECT COUNT(*) AS "Female Influenced by Parents"
FROM learning_aspirations l
INNER JOIN personalized_info p ON p.ResponseID = l.ResponseID
WHERE l.CareerInfluenceFactor = 'My Parents'
	AND p.Gender LIKE 'Female%'
	AND p.CurrentCountry = 'India' 

-- Question 5: How many of the Male Gen-Z are influenced by their parents in regards to their career choices from India

SELECT COUNT(*) AS "Males Influenced by Parents"
FROM learning_aspirations l
INNER JOIN personalized_info p ON p.ResponseID = l.ResponseID
WHERE l.CareerInfluenceFactor = 'My Parents'
	AND p.Gender LIKE 'Male%'
	AND p.CurrentCountry = 'India'

-- Question 6: How many of the Male and Female (individually display in 2 different columns, but as part of the same query) Gen-Z are influenced by their parents in regards to their career choices from India

SELECT COUNT(CASE 
			WHEN p.Gender LIKE 'Male%'
				THEN 1
			END) AS Male
	,COUNT(CASE 
			WHEN p.Gender LIKE 'Female%'
				THEN 1
			END) AS Female
FROM personalized_info p
INNER JOIN learning_aspirations l ON p.ResponseID = l.ResponseID
WHERE l.CareerInfluenceFactor = 'My Parents'
	AND p.CurrentCountry = 'India';

-- Question 7: How many Gen-Z are influenced by Social Media and Influencers together from India

SELECT COUNT(CASE 
			WHEN l.CareerInfluenceFactor LIKE 'Social%'
				THEN 1
			END) AS SocialMedia
	,COUNT(CASE 
			WHEN l.CareerInfluenceFactor LIKE 'Influencers%'
				THEN 1
			END) AS Influencers
FROM learning_aspirations l
INNER JOIN personalized_info p ON p.ResponseID = l.ResponseID
WHERE p.CurrentCountry = 'India';

 -- Question 8: How many Gen-Z are influenced by Social Media and Influencers together, display for Male and Female separately from India

SELECT CareerInfluenceFactor
	,COUNT(CASE 
			WHEN p.Gender LIKE 'Male%'
				THEN 1
			END) AS Male
	,COUNT(CASE 
			WHEN p.Gender LIKE 'Female%'
				THEN 1
			END) AS Female
FROM personalized_info p
INNER JOIN learning_aspirations l ON p.ResponseID = l.ResponseID
WHERE p.CurrentCountry = 'India'
	AND l.CareerInfluenceFactor IN (
		'Social Media like LinkedIn'
		,'Influencers who had successful careers'
		)
GROUP BY l.CareerInfluenceFactor;

-- Question 9: How many of the Gen-Z who are influenced by the social media for their career aspiration are looking to go abroad

SELECT COUNT(*) AS "Influenced by Social Media"
FROM learning_aspirations
WHERE HigherEducationAbroad LIKE 'Yes%'
	AND CareerInfluenceFactor LIKE 'Social%' 

-- Question 10: How many of the Gen-Z who are influenced by "people in their circle" for career aspiration are looking to go abroad

SELECT COUNT(*) AS "influenced by 'people in their circle'"
FROM learning_aspirations
WHERE HigherEducationAbroad LIKE 'Yes%'
	AND CareerInfluenceFactor LIKE '%circle%'
   ```
***

Advance Query
````sql
-- Question 1: What Percentage of Male and Female wants to go to office everyday?

SELECT  
   concat( ROUND((COUNT(CASE WHEN p.Gender like 'Male%' THEN 1 END) / COUNT(*)) * 100, 2), '%') AS Male_aspirants_perc,
   concat( ROUND((COUNT(CASE WHEN p.Gender like 'Female%' THEN 1 END) / COUNT(*)) * 100, 2),'%') AS Female_aspirants_perc
FROM personalized_info p
INNER JOIN learning_aspirations l
ON p.ResponseID = l.ResponseID
WHERE l.PreferredWorkingEnvironment like 'Every Day%';


-- Question 2: What percentage of GenZ's who have choosen their career in business operations are most likely to be influenced by their parents?

SELECT  
    ROUND((COUNT(CASE WHEN ClosestAspirationalCareer like 'Business Operations%' THEN 1 END) / COUNT(*)) * 100, 2) AS Genz_aspirants
FROM learning_aspirations 
WHERE CareerInfluenceFactor='My Parents';

-- Question 3: What Percentage of GenZ prefer opting for higher studies, Give a genderwise approach.

SELECT 
     concat(ROUND((COUNT(CASE WHEN p.gender like 'Male%' THEN 1 END) / COUNT(*)) * 100,2), '%') AS Male_aspirants,
     concat(ROUND((COUNT(CASE WHEN p.gender like 'Female%' THEN 1 END) / COUNT(*)) * 100,2),'%') AS Female_aspirants
FROM personalized_info p
INNER JOIN learning_aspirations l
ON p.ResponseID = l.ResponseID
WHERE HigherEducationAbroad like 'Yes%';


-- Question 4: What percentages of GenZ are willing and not willing to work for a company whose missions are misaligned with their public actions and even their products? (Give Gender Wise Split)

SELECT 
      ROUND((COUNT(CASE WHEN p.gender like 'Male%' AND MisalignedMissionLikelihood like 'Will work%' THEN 1 END) / COUNT(*)) * 100,2) AS Male_willingtowork,
      ROUND((COUNT(CASE WHEN p.gender like 'Female%' AND MisalignedMissionLikelihood like 'Will work%' THEN 1 END) / COUNT(*)) * 100,2) AS Female_willingtowork,
      ROUND((COUNT(CASE WHEN p.gender like 'Male%' AND MisalignedMissionLikelihood like 'Will NOT work%' THEN 1 END) / COUNT(*)) * 100,2) AS Male_Notwillingtowork,
	  ROUND((COUNT(CASE WHEN p.gender like 'Female%' AND MisalignedMissionLikelihood like 'Will NOT work%' THEN 1 END) / COUNT(*)) * 100,2) AS Female_Notwillingtowork
FROM personalized_info p
INNER JOIN mission_aspirations m
ON p.ResponseID=m.ResponseID;

-- Question 5: What is the most suitable working environment according to GenZ?

SELECT PreferredWorkingEnvironment,COUNT(*) AS most_suitable_workenvironment
FROM learning_aspirations l
INNER JOIN personalized_info p
ON l.ResponseID=p.ResponseID
-- WHERE p.Gender LIKE 'Female%'
GROUP BY PreferredWorkingEnvironment
ORDER BY COUNT(*) DESC
LIMIT 1;

-- Question 6: What is the percentage of Males who expected a salary 5 years >50k and 
-- also work under Employers who appreciates learning but doesnt enable an learning environment?

SELECT
      ROUND((COUNT(CASE WHEN p.gender like 'Male%' THEN 1 END) / COUNT(*)) * 100,2) AS Male_Aspirants_perc
FROM personalized_info p
INNER JOIN mission_aspirations m1 
ON p.ResponseID=m1.ResponseID
INNER JOIN manager_aspirations m2 
ON p.ResponseID=m2.ResponseID
WHERE m1.ExpectedSalary5Years not like '>30k%'
AND m2.PreferredEmployer like '%appreciates%but%enables%' ;

-- Question 7: Calculate Total no of females who aspire to work in their closest aspirational career and have a social impact likelihood of 1 to 5.

SELECT 
	(( COUNT(CASE WHEN p.gender like 'Female%' THEN 1 END)/count(*))*100) AS female_aspirants
FROM personalized_info p
INNER JOIN mission_aspirations m1 ON p.ResponseID=m1.ResponseID
INNER JOIN learning_aspirations l ON p.ResponseID=l.ResponseID
WHERE m1.NoSocialImpactLikelihood between 1 AND 5;

-- Question 8: Retrieve the Male aspirants who are interested in Higher studies abroad and have career influence factor of 'My Parents'

SELECT 
      COUNT(CASE WHEN p.gender like 'Male%' THEN 1 END) AS Male_aspirants
FROM personalized_info p
INNER JOIN learning_aspirations l
ON p.ResponseID=l.ResponseID
WHERE l.CareerInfluenceFactor='My Parents'
AND l.HigherEducationAbroad like 'Yes%';

Question 9: Determine the percentage of gender who have a no social impact likelihood of 8-10 among those who are interested in Higher Education Abroad

SELECT
    ROUND(((CASE WHEN p.Gender like 'Male%' THEN 1 END) / COUNT(*)) * 100, 2) AS Male_aspirants,
    ROUND((COUNT(CASE WHEN p.Gender like 'Female%' THEN 1 END) / COUNT(*)) * 100, 2) AS Female_aspirants
FROM Personalized_info p
INNER JOIN mission_aspirations m1 
ON p.ResponseID=m1.ResponseID
INNER JOIN learning_aspirations l 
ON p.ResponseID=l.ResponseID
WHERE m1.NoSocialImpactLikelihood BETWEEN 8 AND 10
AND l.HigherEducationAbroad like 'Yes%';

Question 12: Give a Detailed Split of the GenZ preferences to work with Teams, 
Data should include Male,Female and overall in counts and also the overall in %. */

SELECT
	COUNT(CASE WHEN p.Gender like 'Male%' THEN 1 END) AS male_aspirants_count,
    COUNT(CASE WHEN p.Gender like 'Female%' THEN 1 END) AS female_aspirants_count,
    ROUND((COUNT(CASE WHEN p.Gender like 'Male%' THEN 1 END) / COUNT(*)) * 100, 2) AS male_aspirants_perc,
    ROUND((COUNT(CASE WHEN p.Gender like 'Female%' THEN 1 END) / COUNT(*)) * 100, 2) AS female_aspirants_perc
FROM Personalized_info p
INNER JOIN manager_aspirations m2 
ON p.ResponseID=m2.ResponseID
WHERE m2.PreferredWorkSetup like '%Team%';

-- Question 11: Give a detailed breakdown of worklikelihood3years for each gender

SELECT m2.WorkLikelihood3Years,
    COUNT(CASE WHEN p.Gender like 'Male%' THEN 1 END) AS male_count,
    COUNT(CASE WHEN p.Gender like 'Female%' THEN 1 END) AS female_count 
FROM Personalized_info p
INNER JOIN manager_aspirations m2
ON p.ResponseID = m2.ResponseID
GROUP BY m2.WorkLikelihood3Years;

````
