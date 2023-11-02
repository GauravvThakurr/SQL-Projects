# Gen-Z SQL Survey Analysis

## Schema 

<img width="657" alt="image" src="https://github.com/GauravvThakurr/SQLCodes/assets/141028751/a67ab6ba-bc66-44bd-b8f3-ad6efa80f30e">



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
    
