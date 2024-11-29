# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: TAZEEN QURESHI

```
I am going to answer the prompt step by step.

Step 1: Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

1. Creating a Population of Event Attendees
Code: events = ['wedding'] * 200 + ['brunch'] * 800 (ln 36)
Description: This piece of the code defines a population of 1,000 people attending two types of events: 200 at weddings and 800 at brunches. We are using this in which infections and tracing will take place.
Sampling Frame: The entire group of event attendees (1,000), divided into two event types (weddings and brunch), makes up for the initial sampling frame. This frame is the same as in the article's: weddings are larger gatherings and brunches are smaller but more numerous.
Purpose: The initial population includes high-attendance (weddings) and low-attendance (brunches) incidents/events, it is setting up a simulation where contact tracing will observe infections differently depending on event size.

2. Random Selection of Individuals (Simple Random Sampling)
Code: infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False) (ln 47)
Sampling Procedure: This piece of code uses simple random sampling without replacement, 10% of the attendees (100 of the 1,000 individuals) are randomly marked as infected (ATTACK_RATE = 0.10). Each individual in the population has an equal probability of being selected. This will also ensure an unbiased distribution of infections or infected individuals across both event types.
Sample Size: 100 individuals or 10% of the population.
Underlying Distribution: Uniform random distribution for infection selection.
Purpose: This code sets up a baseline infection rate across weddings and brunches. By infecting individuals from both events at the same rate, we are creating a true proportion of infected cases for each event type before any tracing bias can come in.

3. Primary Contact Tracing (Random)
Code: ppl.loc[ppl['infected'], 'traced'] = np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS (ln 51)
Sampling Procedure: This piece of code takes the infected individuals in the population and applies a 20% change that each one will be traced (or identified as a contact through contact tracing). The probability of tracing an infected person is set by TRACE_SUCCESS = 0.20 or 20%.
For each infected person, a random number between 0 and 1 is generated using the NumPy function np.random.rand. If this number is less than 0.20, the individual is traced but if it is more than 0.20 they are not traced.
Sample Size: 20 individuals are traced out of the 100 infected.
Purpose: This code deliberately does not use 80% of the infected population, meaning that this sample is incomplete. The missing cases could be from harder-to-trace settings, such as smaller gatherings or venues with limited information.
This is reflective of real-worl scenarios (and the article) where contract tracing often fails to identify cases because of issues like limited resources or people's inability to remember all individuals they came into contact with.
Because only a subset of the infected population is being traced here, this sample is biased. This means that the contact tracing sample is not representative of the infected population. 


4. Secondary Contact Tracing (Cluster)
Code: event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts() (ln 54)
Sampling Frame: Events where at least two people have been traced (based on the SECONDARY_TRACE_THRESHOLD = 2) are flagged for secondary contact tracing.
Sampling Procedure: When an event reached secondary trace threshhold of 2, secondary contact tracing process is triggered. 
This means all infected individuals at that event are marked as traced, creating a cluster. In the article, this shows it is easier to trace infections at certain types of events with fixed attendee lists (like weddings).
Purpose: This step imposes a bias towards larger, more easily traced gatherings, such as weddings with guest lists. As a result, it creates a skewed representation of infection sources by focusing on high-attendance events (or over-represents infections at high-attendance events). As the article says, settings like weddings are more "traceable," whereas gatherings at smaller, casual events (like brunches) are harder to trace due to resource constraints and limited information.

Output: The histogram output by this code shows that the proportion of infections traced to weddings is over-represented, which can be misleading that weddings are a primary source of infections. This is in line with the article's argument that the bias in contact tracing can cause an overestimations of infection sources, especially in events that are traceable.

Step 2: Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

The code does not reproduce the graphs from the original blog post. 

The graph in the article: shows the true proportion centered tightly around 20%, which is the actual share of infections from the weddings. The observed proportion has a wider spread and a higher mean (between 40% and 60%). This shows that the observed share of infections is higher than in the true proportion because of the contact tracing bias. This is in line with the article's point that contact tracing skews the perceived importance of easily traceable events like weddings, which resulsts in a higher observed share of cases atributed to them than the true share.

The graph generated by whitby_covid_tracing.py: shows the true proportion represents the share of actual infections from weddings (20%) and the observed proportion has a mean a little higher than 20% but it is not as high as 40%-60%. This graph has some bias towards observed proportion but the magnitude of the bias is lower than the article. 

Step 3: Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

When I reduce the number of repetitions to 1,000, the graphs show slightly more variability with each run. Both groups (true and observed proportions) show differences in shape across the output graphs, with observed proportions being less consistent from one run to the next. The distributions for the observed proportions also display more variability in width and skew.

This variability is expected with a smaller number of repetitions, because any minor change in random sampling at each run can have a bigger effect on the results. As a result, the output graphs become less reliable and more variable. On the other hand, a higher number of repetitions gives me more stable and consistent results, improving the reproducibility of the observed proportions.

Step 4: Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

The answer to this prompt is in ln 15 and ln 16 of the whitby_covid_tracing.py file. I have set the random seed to a fixed value with a np.random.seed(42) which will make sure that everytime the script is run, the sequence of numbers np.random generates is the same. 
The effect on reproducibility is that every run of the script will produce the same output. Without a fixed seed, random selections of infected individuals and traced cases will be different on each run. 
Using fixed value of 42 because it was Jesus's favourite seed number lol - he had a deeper explanation for it that I don't entirely remember right now (Jesus, the instructor for Production module).
```


## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `HH:MM AM/PM - DD/MM/YYYY`
* The branch name for your repo should be: `sampling-and-reproducibility`
* What to submit for this assignment:
    * This markdown file (sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [x ] Create a branch called `sampling-and-reproducibility`.
- [x ] Ensure that the repository is public.
- [x ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [x ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack at `#cohort-3-help`. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
