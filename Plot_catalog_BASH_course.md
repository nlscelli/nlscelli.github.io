# Plotting our results:
Here we'll import our newly created data and plot them. Import the data on the left panel, in the "Files" session and "upload to session storage".

Then put the name of your file in the single quotes in "read_csv" and execute the code.


```python
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns

#- read in the file
df = pd.read_csv('test.out',sep=r"\s+")

#- plot data points
ax = sns.scatterplot(data=df,x=df.columns[0],y=df.columns[1],hue=df.columns[2],palette='RdBu');

#- make colorbar and prettify
norm = plt.Normalize(df[df.columns[2]].min(), df[df.columns[2]].max())
sm = plt.cm.ScalarMappable(cmap="RdBu", norm=norm)
sm.set_array([])
ax.get_legend().remove()
cbar = ax.figure.colorbar(sm)
cbar.ax.set_ylabel(df.columns[2])

plt.show()
```


    
![png](Plot_catalog_BASH_course_files/Plot_catalog_BASH_course_1_0.png)
    



```python

```
