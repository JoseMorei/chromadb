# chromadb

Similarity Search on Employee Records using Python and Chroma DB

- Create a collection for employee data and store related data in a Chroma DB collection.
- Generate numeric representations for specific key-value pairs of the employee dataset and convert them into vector embeddings.
- Store related data in a Chroma DB collection.
- Perform similarity search on vector embeddings that you create and display similar results based on a query.


```
python3 -m venv /Users/jose.moreira/git/chromadb/.venv 
/Users/jose.moreira/git/chromadb/.venv/bin/python -m pip install --upgrade pip
/Users/jose.moreira/git/chromadb/.venv/bin/pip install chromadb==1.0.12
/Users/jose.moreira/git/chromadb/.venv/bin/pip install sentence-transformers==4.1.0
```

```
(.venv) jose.moreira@jose chromadb %   python similarity_employeedata.py
Collection created: employee_collection
Collection contents:
Number of documents: 15
=== Similarity Search Examples ===

1. Searching for Python developers:
Failed to send telemetry event CollectionQueryEvent: capture() takes 1 positional argument but 3 were given
Query: 'Python developer with web development experience'
  1. John Doe (employee_1) - Distance: 0.5156
     Role: Software Engineer, Department: Engineering
     Document: Software Engineer with 5 years of experience in Engineering. Skills: Python, JavaScript, React, Node...
  2. Matthew Garcia (employee_14) - Distance: 0.5724
     Role: Junior Software Engineer, Department: Engineering
     Document: Junior Software Engineer with 3 years of experience in Engineering. Skills: JavaScript, HTML/CSS, ba...
  3. Alex Rodriguez (employee_10) - Distance: 0.5967
     Role: Lead Software Engineer, Department: Engineering
     Document: Lead Software Engineer with 18 years of experience in Engineering. Skills: Full-stack development, R...

2. Searching for leadership and management roles:
Query: 'team leader manager with experience'
  1. Jane Smith (employee_2) - Distance: 0.5382
     Role: Marketing Manager, Experience: 8 years
  2. Sarah Clark (employee_7) - Distance: 0.5467
     Role: HR Manager, Experience: 8 years
  3. David Lee (employee_6) - Distance: 0.5497
     Role: Engineering Manager, Experience: 15 years

=== Metadata Filtering Examples ===

3. Finding all Engineering employees:
Found 8 Engineering employees:
  - John Doe: Software Engineer (5 years)
  - Michael Brown: Senior Software Engineer (12 years)
  - David Lee: Engineering Manager (15 years)
  - Chris Evans: Senior Architect (20 years)
  - Alex Rodriguez: Lead Software Engineer (18 years)
  - Kevin Martinez: DevOps Engineer (10 years)
  - Matthew Garcia: Junior Software Engineer (3 years)
  - Olivia Moore: Principal Engineer (12 years)

4. Finding employees with 10+ years experience:
Found 6 senior employees:
  - Michael Brown: Senior Software Engineer (12 years)
  - David Lee: Engineering Manager (15 years)
  - Chris Evans: Senior Architect (20 years)
  - Alex Rodriguez: Lead Software Engineer (18 years)
  - Kevin Martinez: DevOps Engineer (10 years)
  - Olivia Moore: Principal Engineer (12 years)

5. Finding employees in California:
Found 3 employees in California:
  - Jane Smith: Los Angeles
  - Michael Brown: San Francisco
  - Olivia Moore: San Francisco

=== Combined Search: Similarity + Metadata Filtering ===

6. Finding senior Python developers in major tech cities:
Query: 'senior Python developer full-stack' with filters (8+ years, major tech cities)
Found 4 matching employees:
  1. Michael Brown (employee_4) - Distance: 0.6726
     Senior Software Engineer in San Francisco (12 years)
     Document snippet: Senior Software Engineer with 12 years of experience in Engineering. Skills: Jav...
  2. Chris Evans (employee_8) - Distance: 0.7537
     Senior Architect in New York (20 years)
     Document snippet: Senior Architect with 20 years of experience in Engineering. Skills: System desi...
  3. David Lee (employee_6) - Distance: 0.8344
     Engineering Manager in Seattle (15 years)
     Document snippet: Engineering Manager with 15 years of experience in Engineering. Skills: Team lea...
  4. Olivia Moore (employee_15) - Distance: 0.8761
     Principal Engineer in San Francisco (12 years)
     Document snippet: Principal Engineer with 12 years of experience in Engineering. Skills: Technical...
Top 3 similar documents to "senior Python developer full-stack":
 - ID: employee_4, Text: "Senior Software Engineer with 12 years of experience in Engineering. Skills: Java, Spring Boot, microservices, cloud architecture, DevOps. Located in San Francisco. Employment type: Full-time.", Score: 0.6726
 - ID: employee_8, Text: "Senior Architect with 20 years of experience in Engineering. Skills: System design, distributed systems, cloud platforms, technical strategy. Located in New York. Employment type: Full-time.", Score: 0.7537
 - ID: employee_6, Text: "Engineering Manager with 15 years of experience in Engineering. Skills: Team leadership, project management, software architecture, mentoring. Located in Seattle. Employment type: Full-time.", Score: 0.8344
```

=== Similarity Search Examples === Key Observations

- Semantic Understanding: For the Python query, John Doe ranks highest (0.5156) because his skills explicitly include "Python, JavaScript, React, Node.js" - a perfect match for web development. Notice how the system found relevant candidates even though the query didn't exactly match the text.
- Cross-Department Results: The leadership search finds managers across different departments (Marketing, HR, Engineering), showing the system understands that "manager" roles exist in various contexts.
- Ranking Logic: Distance scores increase (0.5382 → 0.5467 → 0.5497) as semantic similarity decreases, demonstrating how Chroma DB quantifies relevance.
- Skills vs. Roles: Matthew Garcia appears in Python results despite his skills being "JavaScript, HTML/CSS" because his role as a "Software Engineer" is semantically related to development work.

=== Metadata Filtering Examples === Key Observations:

- Exact Filtering Power: The Engineering department filter returns exactly 8 out of 15 employees, showing that over half the workforce is in Engineering - a typical distribution for tech companies.
- Seniority Distribution: The 10+ years filter reveals 6 senior employees (40% of workforce), indicating a good mix of experience levels. Notice how roles reflect seniority: "Senior," "Lead," "Principal," and "Manager" titles.
- Role Hierarchy Visible: In the senior employees list, you can see the career progression: Software Engineer → Senior Software Engineer → Lead/Principal Engineer → Engineering Manager → Senior Architect.
- Geographic Concentration: Only 3 employees in California highlight the distributed nature of the workforce, with San Francisco having multiple employees as expected for a tech hub.
- Perfect Precision: Unlike similarity search, metadata filtering gives 100% precise results - every employee returned exactly matches the specified criteria.

=== Combined Search: Similarity + Metadata Filtering === Key Observations:

- Filtered Pool: From 15 total employees, only 4 meet both the semantic similarity AND the exact criteria (8+ years in major tech cities), demonstrating how filters narrow down results effectively.
- Semantic Ranking within Constraints: Michael Brown ranks highest (0.6726) because his Java/Spring Boot skills are semantically closest to "senior Python developer full-stack" among the filtered candidates.
- Interesting Discoveries: Chris Evans (Senior Architect) ranks second despite having "System design, distributed systems" skills rather than Python - showing the system understands that senior architects often have full-stack capabilities.
- Geographic + Experience Filtering: All results are from San Francisco, New York, or Seattle (major tech cities) and have 10+ years experience, proving the metadata filters worked perfectly.
- Real-World Relevance: This type of search mimics actual HR/recruiting scenarios where you need "senior developers with specific skills in key locations" - combining fuzzy skill matching with hard requirements.
- Distance Score Interpretation: Higher distances (0.8761 for Olivia) indicate she's still relevant to the query but with a looser semantic match, which is valuable for finding candidates with transferable skills.
