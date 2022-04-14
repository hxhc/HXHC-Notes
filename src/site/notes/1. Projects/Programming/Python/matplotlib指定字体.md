---
{"dg-publish":true,"dg-permalink":"matplotlib-font","permalink":"/matplotlib-font/"}
---

## Use customized font along with matplotlib
check [matplotlib set_title](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.axes.Axes.set_title.html), [matplotlib Text](https://matplotlib.org/3.1.1/api/text_api.html#matplotlib.text.Text) and [matplotlib font_manager](https://matplotlib.org/3.1.1/api/font_manager_api.html#matplotlib.font_manager)
also check [stackoverflow](https://stackoverflow.com/questions/39630928/how-to-plot-a-figure-with-chinese-characters-in-label)

```python
plt.style.use(["zs"])
title_fontP = font_manager.FontProperties()
title_fontP.set_family('Microsoft Yahei')
title_fontP.set_size(28)
fig2 = plt.figure(figsize=(16, 18))
fig2.subplots_adjust(hspace=0.3, wspace=0.2)
ax2 = fig2.subplots(3, 2)
ax2[0, 0].plot(pure_spectra_2[:, 0], pure_spectra_2[:, 1])
ax2[0, 0].set_title("纯物质光谱", fontproperties=title_fontP)
ax2[0, 1].plot(wavelength, raw_data.T, lw=1)
ax2[0, 1].set_title("原始光谱", fontfamily="Microsoft Yahei")
ax2[1, 0].plot(wavelength, as_data.T, lw=1)
ax2[1, 0].set_title("AutoScale")
ax2[1, 1].plot(wavelength, snv_data.T, lw=1)
ax2[1, 1].set_title("SNV")
ax2[2, 0].plot(wavelength, sg1st_data.T, lw=1)
ax2[2, 0].set_title("SG-1st")
plt.delaxes(ax2[2, 1])

```