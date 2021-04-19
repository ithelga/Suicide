import pandas as pd
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression

hdi_2015 = pd.read_csv('HDI_2015.csv', delimiter=',', index_col='Country')
hdi_2010_2014 = pd.read_csv('HDI_2010_2014.csv', delimiter=',', index_col='Country')
hdi_2010_2014 = hdi_2010_2014[['2012', '2013', '2014']]
hdi_2010_2014.columns = ['HDI 2012', 'HDI 2013', 'HDI 2014']
hdi_2015 = hdi_2015['2015']
hdi_2015.name = 'HDI 2015'
hdi = pd.merge(hdi_2010_2014, hdi_2015, left_on='Country', right_on='Country')

hr_2010_2015 = pd.read_csv('HR_2005_2019.csv', delimiter=',')
hr_2010_2015 = hr_2010_2015[['Country', 'year', 'HR']]
hr_2010_2015 = hr_2010_2015[hr_2010_2015['year'].isin(['2012', '2013', '2014', '2015'])]
d = {c['Country']: [] for i, c in hr_2010_2015.iterrows()}
for i, c in hr_2010_2015.iterrows():
    d[c['Country']].append(float('{0:.7f}'.format(c['HR'])))
a = [k for k, v in d.items() if len(v) < 4]
for i in a:
    del d[i]
hr = pd.DataFrame.from_dict(d, orient='index')
hr.columns = ['HR 2012', 'HR 2013', 'HR 2014', 'HR 2015']

sui = pd.read_csv('suicide_1985_2016.csv', delimiter=',')
sui = sui[['country', 'year', 'suicides/100k pop']]
sui = sui[sui['year'].isin(['2012', '2013', '2014', '2015'])]
d = {k: [] for k in d.keys()}
for i, c in sui.iterrows():
    if c['country'] in d.keys():
        d[c['country']].append([str(c['year']), float(c['suicides/100k pop'])])
a = [k for k, v in d.items() if v == []]
for i in a:
    del d[i]
m = {}

for k, v in d.items():
    p = {'2012': 0, '2013': 0, '2014': 0, '2015': 0}
    for y, z in v:
        p[y] += z
    for i, q in p.items():
        p[i] = float('{0:.2f}'.format(q / 12))
    d[k] = [i for i in p.values()]

a = [k for k, v in d.items() if 0.0 in v]
for i in a:
    del d[i]
n = range(len(d) - 1)
suicide = pd.DataFrame.from_dict(d, orient='index')
suicide.columns = ['D 2012', 'D 2013', 'D 2014', 'D 2015']
hr_sui = pd.merge(hr, suicide, left_index=True, right_index=True)
global_s = pd.merge(hdi, hr_sui, left_index=True, right_index=True)


def set_pandas_display_options() -> None:
    display = pd.options.display
    display.max_columns = 12
    display.max_rows = 111
    display.width = None


set_pandas_display_options()
print(global_s)

global_s.plot.bar(title="Sample of 46 countries from all over the world")
global_s.plot.bar(title="Sample of 46 countries from all over the world", subplots=True)

list_hdi_J = [float('{0:.3f}'.format(a)) for a in hdi.loc['Italy']]
list_hdi_I = [float('{0:.3f}'.format(a)) for a in hdi.loc['Japan']]
list_hdi_US = [float('{0:.3f}'.format(a)) for a in hdi.loc['United States']]
list_hr_J = [float('{0:.3f}'.format(a)) for a in hr.loc['Italy']]
list_hr_I = [float('{0:.3f}'.format(a)) for a in hr.loc['Japan']]
list_hr_US = [float('{0:.3f}'.format(a)) for a in hr.loc['United States']]
list_D_J = [float('{0:.3f}'.format(a)) for a in suicide.loc['Italy']]
list_D_I = [float('{0:.3f}'.format(a)) for a in suicide.loc['Japan']]
list_D_US = [float('{0:.3f}'.format(a)) for a in suicide.loc['United States']]

print(f'\nIn Japan (2012-2015): \n1) the suicide rate per 100 thousand population is {list_D_J},'
      f'\n2)the index of happiness is {list_hr_J},'
      f'\n3)the index of development{list_hdi_J}')
print(f'\nIn United States(2012-2015):\n1) the suicide rate per 100 thousand population is {list_D_US},'
      f'\n2)the index of happiness is {list_hr_US},'
      f'\n3)the index of development{list_hdi_US}')
print(f'\nIn Italy(2012-2015): \n1)the suicide rate per 100 thousand population is {list_D_I},'
      f'\n2)the index of happiness is {list_hr_I},'
      f'\n3)the index of development{list_hdi_I}')

YEARS = [2012, 2013, 2014, 2015]

title1_D = 'Suicide coefficient'
fig_D, (ax_Italy_D, ax_Japan_D, ax_US_D) = plt.subplots(nrows=3, ncols=1, figsize=(10, 15))
ax_Italy_D.set_title(title1_D)
ax_Italy_D.plot(YEARS, list_D_I)
ax_Italy_D.set_ylabel('in Italy')
ax_Japan_D.plot(YEARS, list_D_J)
ax_Japan_D.set_ylabel('in Japan')
ax_US_D.plot(YEARS, list_D_US)
ax_US_D.set_ylabel('in United States')

title1_hdi = 'Human development rate'
fig_hdi, (ax_Italy_hdi, ax_Japan_hdi, ax_US_hdi) = plt.subplots(nrows=3, ncols=1, figsize=(10, 15))
ax_Italy_hdi.set_title(title1_hdi)
ax_Italy_hdi.plot(YEARS, list_hdi_I)
ax_Italy_hdi.set_ylabel('in Italy')
ax_Japan_hdi.plot(YEARS, list_hdi_J)
ax_Japan_hdi.set_ylabel('in Japan')
ax_US_hdi.plot(YEARS, list_hdi_US)
ax_US_hdi.set_ylabel('in United States')

title1_hr = 'Happiness rate'
fig_hr, (ax_Italy_hr, ax_Japan_hr, ax_US_hr) = plt.subplots(nrows=3, ncols=1, figsize=(10, 15))
ax_Italy_hr.set_title(title1_hr)
ax_Italy_hr.plot(YEARS, list_hr_I)
ax_Italy_hr.set_ylabel('in Italy')
ax_Japan_hr.plot(YEARS, list_hr_J)
ax_Japan_hr.set_ylabel('in Japan')
ax_US_hr.plot(YEARS, list_hr_US)
ax_US_hr.set_ylabel('in United States')

df_US = pd.DataFrame({'HDI': list_hdi_US,
                      'HR': list_hr_US,
                      'D': list_D_US}, index=YEARS)
ax_US = df_US.plot.bar(rot=0, title="Ratio of indicators in United States")

df_I = pd.DataFrame({'HDI': list_hdi_I,
                     'HR': list_hr_I,
                     'D': list_D_I}, index=YEARS)
ax_I = df_I.plot.bar(rot=0, title="Ratio of indicators in Italy")

df_J = pd.DataFrame({'HDI': list_hdi_J,
                     'HR': list_hr_J,
                     'D': list_D_J}, index=YEARS)
ax_J = df_J.plot.bar(rot=0, title="Ratio of indicators in Japan")

x_2012 = global_s[['HDI 2012', 'HR 2012']]
x_2012.columns = ['HDI', 'HR']
x_2013 = global_s[['HDI 2013', 'HR 2013']]
x_2013.columns = ['HDI', 'HR']
x_2014 = global_s[['HDI 2014', 'HR 2014']]
x_2014.columns = ['HDI', 'HR']
x_2015 = global_s[['HDI 2015', 'HR 2015']]
x_2015.columns = ['HDI', 'HR']
x = pd.concat([x_2012, x_2013, x_2014, x_2015], ignore_index=True)

y_2012 = global_s[['D 2012']]
y_2012.columns = ['D']
y_2013 = global_s[['D 2013']]
y_2013.columns = ['D']
y_2014 = global_s[['D 2014']]
y_2014.columns = ['D']
y_2015 = global_s[['D 2015']]
y_2015.columns = ['D']
y = pd.concat([y_2012, y_2013, y_2014, y_2015], ignore_index=True)
model = LinearRegression().fit(x, y)

hr_2016_2019 = pd.read_csv('HR_2005_2019.csv', delimiter=',')
hr_2016_2019 = hr_2016_2019[['Country', 'year', 'HR']]
hr_2016_2019 = hr_2016_2019[hr_2016_2019['year'].isin(['2016', '2017', '2018', '2019'])]
hr_2016_2019 = hr_2016_2019[hr_2016_2019['Country'].isin(['Italy', 'Japan', 'United States'])]
d = {c['Country']: [] for _, c in hr_2016_2019.iterrows()}

for i, c in hr_2016_2019.iterrows():
    d[c['Country']].append(float('{0:.7f}'.format(c['HR'])))

hr_2016_2019 = pd.DataFrame.from_dict(d, orient='index')
hr_2016_2019.columns = ['HR 2016', 'HR 2017', 'HR 2018', 'HR 2019']
hdi_2016_2019 = pd.read_csv('HDI_2016_2019.csv', delimiter=',', index_col='Country')
hdi_2016_2019.columns = ['HDI 2016', 'HDI 2017', 'HDI 2018', 'HDI 2019']
glob_new = pd.merge(hdi_2016_2019, hr_2016_2019, left_index=True, right_index=True)
x_2016 = glob_new[['HDI 2016', 'HR 2016']]
x_2017 = glob_new[['HDI 2017', 'HR 2017']]
x_2018 = glob_new[['HDI 2018', 'HR 2018']]
x_2019 = glob_new[['HDI 2019', 'HR 2019']]

y_2016 = model.predict(x_2016)
y_2017 = model.predict(x_2017)
y_2018 = model.predict(x_2018)
y_2019 = model.predict(x_2019)

NEW_YEARS = [2016, 2017, 2018, 2019]
predict_Italy_date = [round(y_2016[0][0], 2), round( y_2017[0][0], 2), round(y_2018[0][0], 2), round(y_2019[0][0], 2)]
predict_Japan_D = [round(y_2016[1][0], 2), round(y_2017[1][0], 2), round(y_2018[1][0], 2), round(y_2019[1][0], 2)]
predict_US_D = [round(y_2016[2][0], 2), round(y_2017[2][0], 2), round(y_2018[2][0], 2), round(y_2019[2][0], 2)]

print(f'\nIn Japan (2016-2019): \nthe predicted suicide coefficient  '
      f'per 100 thousand population is {predict_Japan_D},')

print(f'In United States(2016-2019):\nthe predicted suicide coefficient '
      f'per 100 thousand population is {predict_US_D},')

print(f'In Italy(2016-2019): \nthe predicted suicide coefficient '
      f'per 100 thousand population is {predict_Italy_date}')

df = pd.DataFrame({'Italy': predict_Italy_date,
                   'Japan': predict_Japan_D,
                   'US': predict_US_D}, index=NEW_YEARS)
ax = df.plot.bar(rot=0, title="Predicted data")

fig_new_D, (new_Italy_D, new_Japan_D, new_US_D) = plt.subplots(nrows=3, ncols=1, figsize=(7, 10))
new_Italy_D.set_title("Predicted suicide coefficient ")
new_Italy_D.plot(NEW_YEARS, predict_Italy_date)
new_Italy_D.set_ylabel('in Italy')
new_Japan_D.plot(NEW_YEARS, predict_Japan_D)
new_Japan_D.set_ylabel('in Japan')
new_US_D.plot(NEW_YEARS, predict_US_D)
new_US_D.set_ylabel('in United States')

title1_D_all = 'Suicide coefficient before and after predicted'
fig_D_all, (ax_Italy_D_all, ax_Japan_D_all, ax_US_D_all) = plt.subplots(nrows=3, ncols=1, figsize=(10, 15))
ax_Italy_D_all.set_title(title1_D_all)
ax_Italy_D_all.plot(YEARS + NEW_YEARS, list_D_I + predict_Italy_date)
ax_Italy_D_all.set_ylabel('in Italy')
ax_Japan_D_all.plot(YEARS + NEW_YEARS, list_D_J + predict_Japan_D)
ax_Japan_D_all.set_ylabel('in Japan')
ax_US_D_all.plot(YEARS + NEW_YEARS, list_D_US + predict_US_D)
ax_US_D_all.set_ylabel('in United States')

plt.show()
