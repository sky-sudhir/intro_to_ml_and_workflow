# Challenge: Which combination of features is most associated with survival?

The combination of **Passenger Class (Pclass)**, **Sex**, and **Age Group** appears to be most strongly associated with survival. While each of these factors is a strong predictor on its own, their interaction reveals a clear social hierarchy in who survived. The data suggests a "women and children first" protocol that was heavily stratified by wealth and class.

---

_Note: The following code assumes you have a pandas DataFrame named `df` that has already been processed to include the `AgeGroup` column as created in the previous steps._

### Analysis Step 1: The Impact of Sex and Class

First, let's establish the combined impact of the two most powerful features: `Sex` and `Pclass`. A pivot table clearly shows that being female dramatically increased survival chances, but being in a higher class amplified this effect significantly. A female in 1st class had a 96.8% chance of survival, whereas a female in 3rd class had only a 50% chance. The outlook for males was grim across all classes, but especially in 3rd class.

```python
# Create a pivot table showing survival rates for Pclass and Sex
survival_by_class_sex = df.pivot_table(values='Survived', index='Pclass', columns='Sex', aggfunc='mean')

print("--- Survival Rate by Pclass and Sex ---")
# Using .style.format() to display percentages for clarity
display(survival_by_class_sex.style.format('{:.2%}'))
```

### Analysis Step 2: Adding Age to the Equation

Now, let's add our third feature, `AgeGroup`, to see how being a child, adult, or senior further influenced these outcomes. The multi-level `groupby` below shows the survival rates for all three features combined. This table reveals that being a 'Child' offered a significant survival advantage, but primarily for those in 1st and 2nd class. Notice that a male child in 1st class had a 100% survival rate, compared to only a 22% survival rate for a male child in 3rd class.

```python
# Group by Pclass, Sex, and our created AgeGroup to get the most detailed view
survival_by_all_three = df.groupby(['Pclass', 'AgeGroup', 'Sex'])['Survived'].mean().unstack(level='Sex')

print("--- Survival Rate by Pclass, Age Group, and Sex ---")
# Formatting the output to show percentages and handle missing groups
display(survival_by_all_three.style.format('{:.2%}', na_rep="-"))
```

### Visualization: Telling the Story with a Plot

A table of numbers can be dense. A visualization makes the story instantly clear. The plot below shows the survival rate for each combination. The separation between the blue (female) and orange (male) bars is striking. You can also see how the survival rates systematically decrease as you move from Pclass 1 to Pclass 3 across the columns.

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Use catplot to create subplots for each class, showing survival by AgeGroup and Sex
g = sns.catplot(
    data=df,
    x='AgeGroup',
    y='Survived',
    hue='Sex',
    col='Pclass',
    kind='bar',
    palette={'male': 'C1', 'female': 'C0'},
    height=5,
    aspect=0.8
)

g.fig.suptitle('Survival Rate by Age Group, Sex, and Passenger Class', y=1.03)
g.set_axis_labels("Age Group", "Survival Rate")
g.set_titles("Pclass {col_name}")
g.set(ylim=(0, 1))
plt.show()
```

---

### Summary of Findings

The analysis strongly suggests that survival on the Titanic was not a matter of chance, but a reflection of a clear social hierarchy. The most critical factor for survival was being female, followed closely by being in a higher passenger class. **A female in 1st or 2nd class, regardless of age, was the most likely survivor.** The "women and children first" mantra appears to hold, but with a crucial caveat: privilege amplified its effect. A male child in first class was safer than an adult female in third class. Conversely, being a male passenger in 3rd class was the most perilous position, with survival rates below 20% for all age groups, confirming that social standing and gender combined to be the ultimate arbiters of life and death.
