一、项目概述
1.1 项目背景
在大学生活中，课程、作业、社团活动、考试等事务繁多，容易导致时间管理混乱和重要事项遗忘。为了帮助大学生更高效地管理个人日程，我们开发了这个基于 Python 的大学生日程管理系统。

1.2 项目目标
本项目旨在开发一个简单易用的日程管理系统，让大学生能够方便地添加、查看、修改和删除日程安排，同时具备提醒功能，确保重要事项不会被遗忘。

二、功能需求
2.1 日程添加
用户可以添加新的日程，包括日程的名称、开始时间、结束时间、地点和备注等信息。

2.2 日程查看
用户可以查看所有日程，也可以按照日期筛选查看特定日期的日程。

2.3 日程修改
用户可以修改已有的日程信息，如日程名称、时间、地点等。

2.4 日程删除
用户可以删除不需要的日程。

2.5 提醒功能
系统可以在日程开始前一定时间提醒用户，提醒方式可以是弹出消息框。

三、项目结构
3.1 文件结构
schedule_manager/
├── main.py
├── schedule.py
├── reminder.py
├── data/
│   └── schedules.json
main.py：主程序入口，负责与用户交互，调用其他模块的功能。
schedule.py：日程管理模块，负责日程的添加、查看、修改和删除操作。
reminder.py：提醒模块，负责日程提醒功能。
data/schedules.json：用于存储日程数据的文件。
3.2 模块功能
3.2.1 schedule.py
import json

class ScheduleManager:
    def __init__(self, file_path):
        self.file_path = file_path
        try:
            with open(file_path, 'r') as f:
                self.schedules = json.load(f)
        except FileNotFoundError:
            self.schedules = []

    def add_schedule(self, schedule):
        self.schedules.append(schedule)
        self.save_schedules()

    def view_schedules(self, date=None):
        if date:
            return [s for s in self.schedules if s['start_time'].startswith(date)]
        return self.schedules

    def modify_schedule(self, index, new_schedule):
        if 0 <= index < len(self.schedules):
            self.schedules[index] = new_schedule
            self.save_schedules()
            return True
        return False

    def delete_schedule(self, index):
        if 0 <= index < len(self.schedules):
            del self.schedules[index]
            self.save_schedules()
            return True
        return False

    def save_schedules(self):
        with open(self.file_path, 'w') as f:
            json.dump(self.schedules, f, indent=4)
3.2.2 reminder.py
import time
import tkinter as tk
from tkinter import messagebox

def remind(schedule):
    root = tk.Tk()
    root.withdraw()
    messagebox.showinfo("日程提醒", f"您有日程：{schedule['name']} 即将开始！")
    root.destroy()

def check_reminders(schedules):
    current_time = time.time()
    for schedule in schedules:
        start_time = time.mktime(time.strptime(schedule['start_time'], '%Y-%m-%d %H:%M'))
        if start_time - current_time <= 60:  # 提前 1 分钟提醒
            remind(schedule)
3.2.3 main.py
from schedule import ScheduleManager
from reminder import check_reminders
import time

file_path = 'data/schedules.json'
schedule_manager = ScheduleManager(file_path)

while True:
    print("\n大学生日程管理系统")
    print("1. 添加日程")
    print("2. 查看日程")
    print("3. 修改日程")
    print("4. 删除日程")
    print("5. 退出")
    choice = input("请输入你的选择：")

    if choice == '1':
        name = input("日程名称：")
        start_time = input("开始时间（YYYY-MM-DD HH:MM）：")
        end_time = input("结束时间（YYYY-MM-DD HH:MM）：")
        location = input("地点：")
        note = input("备注：")
        schedule = {
            "name": name,
            "start_time": start_time,
            "end_time": end_time,
            "location": location,
            "note": note
        }
        schedule_manager.add_schedule(schedule)
        print("日程添加成功！")
    elif choice == '2':
        date = input("请输入要查看的日期（YYYY-MM-DD，留空查看所有日程）：")
        schedules = schedule_manager.view_schedules(date)
        if schedules:
            for i, schedule in enumerate(schedules):
                print(f"{i}. {schedule}")
        else:
            print("没有找到相关日程。")
    elif choice == '3':
        index = int(input("请输入要修改的日程编号："))
        name = input("新的日程名称：")
        start_time = input("新的开始时间（YYYY-MM-DD HH:MM）：")
        end_time = input("新的结束时间（YYYY-MM-DD HH:MM）：")
        location = input("新的地点：")
        note = input("新的备注：")
        new_schedule = {
            "name": name,
            "start_time": start_time,
            "end_time": end_time,
            "location": location,
            "note": note
        }
        if schedule_manager.modify_schedule(index, new_schedule):
            print("日程修改成功！")
        else:
            print("无效的日程编号。")
    elif choice == '4':
        index = int(input("请输入要删除的日程编号："))
        if schedule_manager.delete_schedule(index):
            print("日程删除成功！")
        else:
            print("无效的日程编号。")
    elif choice == '5':
        break
    else:
        print("无效的选择，请重新输入。")

    # 检查提醒
    check_reminders(schedule_manager.view_schedules())
    time.sleep(1)
四、安装与运行
4.1 安装依赖
本项目使用 Python 标准库，无需额外安装依赖。

4.2 运行项目
确保你已经安装了 Python 3.x。
将项目文件下载到本地。
打开终端，进入项目目录。
运行以下命令启动程序：
python main.py
五、测试计划
5.1 功能测试
日程添加：添加不同类型的日程，检查是否能正确保存到文件中。
日程查看：查看所有日程和特定日期的日程，检查显示结果是否正确。
日程修改：修改已有的日程，检查文件中的数据是否更新。
日程删除：删除日程，检查文件中的数据是否正确删除。
提醒功能：设置一个即将开始的日程，检查是否能在规定时间弹出提醒框。
5.2 边界测试
输入无效的日期格式，检查系统是否能正确处理。
输入超出范围的日程编号，检查系统是否给出错误提示。
六、未来改进方向
增加图形用户界面（GUI），提高用户体验。
支持多种提醒方式，如声音提醒、邮件提醒等。
增加日程分类功能，如课程、作业、活动等。
支持多用户使用，每个用户有独立的日程数据。
