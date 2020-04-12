- version 1: http://www.codeskulptor.org/#user47_V0X3ji97p0DpHxS.py
```python
# inputs:
# original volume of solution, unit (ml)
ori_vol = 50
# original saturability of AS in solution, i.e., 30 percenst
ori_sat = 0
# target saturability of AS in solution, i.e., 40 percents
tar_sat = 30

# output the volume of 100 percents AS to add in solution
# in order to achieve the target saturability
# formula: ori_vol * ori_sat / 100.0 + add_vol = (ori_vol + add_vol) * tar_sat / 100.0
add_vol = (ori_vol * tar_sat - ori_vol * ori_sat) / (100.0 - tar_sat)
print 'You should add %f ml of 100% AS.' % add_vol
print 'The total volume of solution is %f ml.' % (add_vol + ori_vol)
```
