## DAX Measures

Measures reference a table named `HR_data`. Rename to match your own model.

### KPI card measures

The four headline numbers on page one.

```dax
Headcount = COUNTROWS('HR_data')

Avg Salary = AVERAGE('HR_data'[Salary])

Avg Leave Balance = AVERAGE('HR_data'[Leave Balance])
```

### Gender pay gap

(Average male salary − average female salary) / average male salary.
Positive means men earn more on average, negative means women earn more.

```dax
Avg Salary Male = 
CALCULATE(AVERAGE('HR_data'[Salary]), 'HR_data'[Gender] = "Male")

Avg Salary Female = 
CALCULATE(AVERAGE('HR_data'[Salary]), 'HR_data'[Gender] = "Female")

Gender Pay Gap % = 
DIVIDE([Avg Salary Male] - [Avg Salary Female], [Avg Salary Male])
```

Used in 3 places: the KPI card on page one (company-wide), the KPI card on the drill-through page (recalculates per role), and the diverging bar chart on page one, where the same measure is plotted against Job Title to show the gap for all ten roles at once instead of one at a time.

### Cumulative hires by year

```dax
Cummulative headcount = 
    VAR currentDate = LASTDATE(HR_data[Date of Join])
RETURN
    CALCULATE([Headcount], ALL(HR_data[Date of Join]),HR_data[Date of Join]<= currentDate)
```

### Drill-through dynamic title

Shows the currently selected job title at the top of the drill-through page.

```dax
Selected Job Title = SELECTEDVALUE('HR_data'[Job Title])

Drillthrough Title = "Job title: " & COALESCE([Selected Job Title], "All job titles")
```

Left out of the final build. Without an exit or termination date in the source data, this only measures tenure of people currently employed, not actual average length of employment across everyone who's worked here.

### Left out entirely

A waterfall chart was the first version of the gender-pay-gap-by-role visual on page one. It got replaced with a diverging bar chart because the waterfall's staircase shape implied the ten role-level gaps summed to something, which they don't, since each one is an independent `DIVIDE()` against that role's own average male salary.
