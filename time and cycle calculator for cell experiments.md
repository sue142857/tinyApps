# App
this App is to calculate time and cycle for Kai's cell experiments

# platform
CodeSkulptor: http://www.codeskulptor.org/#user47_YZS2SJaduOECg3W.py

# Code
```python
import simplegui

check_input = True
# global variants to calculate time
cur_time = "03/08 13:05"
cur_num = 10
tar_num = 100
cycle_str, cycle = '23:59', 1439 # 23 hours and 59 minutes
wait = 4676
tar_date_time = '03/11 19:01'

# another function to compute cycle
num_start = 10
num_end = 100
time_start = "03/08 13:05"
time_end = '03/11 19:01'
cycle_new = 1439

# day numbers for each month in 2020 year
months = [31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]

# helpers
def days_in_year(s):
    # s format: '03/09'
    global months
    [i, j] = s.split('/')
    return sum(months[:int(i)-1]) + int(j)

def minutes_in_day(s):
    # s format: '15:57'
    [i, j] = s.split(':')
    return int(i)*60 + int(j)

def date_and_time(t):
    # t minutes
    # return '03/09 15:57'
    global months
    d = t//(24*60) + 1
    res = t%(24*60)
    h = res//60
    mints = res%60
    for i in range(12):
        if d - months[i] <=0: break
        d -= months[i]
    m = i+1
    return '%02d/%02d %02d:%02d' % (m, d, h,mints)

#Handlers for input fields
# to do: check inputs and show hints
# to do: tar_num > cur_num > 0, date and time format, cycle_str format
# to do: num_end > num_start > 0, date and time format, time_end > time_start
def current_time_input_handler(t):
    # t format: "03/08 13:05"
    global cur_time
    cur_time = t

def cycle_input_handler(t):
    global cycle_str, cycle
    cycle_str = t
    cycle = minutes_in_day(t)

def current_cell_number_input_handler(n):
    # e | E (scientific notation): Returns a float multiplied by the specified power of 10.
    # 1.1e0 / 1.1e1 / 1.1e2 /1.1e-2 / ...
    global cur_num
    cur_num = int(float(n))
    
def target_cell_number_input_handler(n):
    # e | E (scientific notation): Returns a float multiplied by the specified power of 10.
    # 1.1e0 / 1.1e1 / 1.1e2 /1.1e-2 / ...
    global tar_num
    tar_num = int(float(n))
    
def start_time_input_handler(t):
    # t format: "01/01 00:00"
    global time_start
    time_start = t
    
def end_time_input_handler(t):
    # t format: "01/02 23:59"
    global time_end
    time_end = t

def start_num_input_handler(n):
    global num_start
    num_start = int(float(n))

def end_num_input_handler(n):
    global num_end
    num_end = int(float(n))
    
# Handlers for mouse click  
def calculate_time():
    global check_input
    global cur_time_field, cycle_field, cur_num_field, tar_num_field
    global cur_num, tar_num, cur_time, cycle_str, cycle, wait, tar_date_time
    # get inputs from input fields
    if not cur_num_field.get_text() or not tar_num_field.get_text() or not cur_time_field.get_text() or not cycle_field.get_text(): 
        check_input = False
        return
    else:
        check_input = True
    cur_num = int(float(cur_num_field.get_text()))
    tar_num = int(float(tar_num_field.get_text()))
    cur_time = cur_time_field.get_text()
    cycle_str = cycle_field.get_text()
    cycle = minutes_in_day(cycle_str)
    
    # compute how many cycles are needed
    i, xi = 0, cur_num
    while xi < tar_num:
        i += 1
        pre = xi
        xi *= 2
    cycle_num = i - 1 + float((tar_num - pre)) / float((xi - pre))
    
    # compute how many minutes are needed
    wait = int(cycle * cycle_num//1)
    
    # compute wait until which date and time
    (date3, time3) = cur_time.split()
    pass_in_year = (days_in_year(date3)-1)*24*60 + minutes_in_day(time3) + wait
    tar_date_time = date_and_time(pass_in_year)

def calculate_cycle():
    global check_input
    global time_start_field, time_end_field, num_start_field, num_end_field
    global time_start, time_end, num_start, num_end, cycle_new
    # get inputs from input fields
    if not time_start_field.get_text() or not time_end_field.get_text() or not num_start_field.get_text() or not num_end_field.get_text(): 
        check_input = False
        return
    else:
        check_input = True
    num_start = int(float(num_start_field.get_text()))
    num_end = int(float(num_end_field.get_text()))
    time_start = time_start_field.get_text()
    time_end = time_end_field.get_text()
    
    # compute how many minutes btw two dates
    (date0, time0) = time_start.split()
    (date1, time1) = time_end.split()
    t1 = (days_in_year(date1) - days_in_year(date0))*24*60
    t2 = minutes_in_day(time1) - minutes_in_day(time0)
    t = t1 + t2
    
    # compute how many cycles btw two dates
    i, xi = 0, num_start
    while xi < num_end:
        i += 1
        pre = xi
        xi *= 2
    cycle_num = i - 1 + float((num_end - pre)) / float((xi - pre))
    
    # compute how many minutes one cycle has
    cycle_new = int((t / cycle_num)//1)
    
# Handler to draw on canvas
def draw(canvas):
    global check_input
    if not check_input: 
        canvas.draw_text('Fill all input boxes!', [25,50], 25, "Blue")
        return
    # time function
    global cur_time, tar_date_time, cur_num, tar_num, wait, cycle
    canvas.draw_text('Calculate Time', [25,50], 25, "Blue")
    
    canvas.draw_text('Please wait until ', [25,100], 25, "Black")
    canvas.draw_text(tar_date_time, [200,100], 50, "Red")
    canvas.draw_text('to get %.2e cells' % (tar_num,), [450,100], 25, "Black")
    canvas.draw_text('Operated on %s with %.2e cells' % (cur_time, cur_num), [25,150], 25, "Black")
    
    d, h, m = wait//(24*60), (wait%(24*60)) // 60, (wait%(24*60)) % 60
    h2, m2 = cycle // 60, cycle % 60
    canvas.draw_text('Given a cycle of %d hours and %d minutes, please wait %d days, %d hours and %d minutes' % (h2, m2, d, h, m), [25,200], 25, "Black")
    
    # cycle function
    global time_start, time_end, num_start, num_end, cycle_new
    canvas.draw_text('Calculate Cycle', [25,250], 25, "Blue")
    h, m = cycle_new // 60, cycle_new % 60
    canvas.draw_text('One cycle has ', [25,300], 25, "Black")
    canvas.draw_text('%d hours and %d minutes' % (h, m), [200,300], 50, "Red")
    canvas.draw_text('Started on %s with %.2e cells' % (time_start, num_start), [25,350], 25, "Black")
    canvas.draw_text('Ended on %s with %.2e cells' % (time_end, num_end), [25,400], 25, "Black")
    
# Create a frame and assign callbacks to event handlers
frame = simplegui.create_frame("Time and Cycle Calculator for Cell Experiment", 1000, 500)
# time function
cur_time_field = frame.add_input('Current Time', current_time_input_handler, 100)
cur_num_field = frame.add_input('Current Cell Number', current_cell_number_input_handler, 130)
tar_num_field = frame.add_input('Target Cell Number', target_cell_number_input_handler, 130)
cycle_field = frame.add_input('Cycle', cycle_input_handler, 100)
frame.add_label('',1)
frame.add_button("Calculate Time", calculate_time)
frame.add_label('',1)
# cycle function
time_start_field = frame.add_input('Start Time', start_time_input_handler, 100)
time_end_field = frame.add_input('End Time', end_time_input_handler, 100)
num_start_field = frame.add_input('Start Cell Number', start_num_input_handler, 100)
num_end_field = frame.add_input('End Cell Number', end_num_input_handler, 100)
frame.add_label('',1)
frame.add_button("Calculate Cycle", calculate_cycle)
# draw
frame.set_draw_handler(draw)
frame.set_canvas_background('White')
# Start the frame animation
frame.start()

```
