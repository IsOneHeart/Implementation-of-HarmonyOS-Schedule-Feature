# Implementation of HarmonyOS Schedule Feature
## I. Introduction and Preparation
Overall Introduction

This project focuses on implementing the schedule feature in HarmonyOS, utilizing environments such as HarmonyOS 5.0.0 and above, along with DevEco Studio 5.0.0 and above.

An overview of the application's overall layout design is provided, including the configuration of the schedule module (ScheduleView.ets) and the key code implementation for the schedule entry UI in the pages>MainEntry.ets file under the products layer. This demonstrates how to integrate the calendar module component, construct a navigation container, and implement tab switching for the bottom navigation bar.

The schedule functionality module and its implementation process are elaborated in detail. The UI structure of the schedule module consists of core components such as the main schedule view (supporting switching between monthly and weekly views, with highlighting of today's schedule), the schedule details page (displaying time, location, participants, and reminder settings), and the add/edit page (providing form input, map location selection, and attachment upload functions). Special emphasis is placed on the creation and implementation code of the ScheduleView.ets file, including loading schedule data, implementing search functionality, and adding new schedules.

The development of calendar components is also introduced, including the code implementation of the calendar header (ScheduleTitle.ets, which implements functions like year-month switching and lunar calendar toggle) and the calendar date view (CalenderView.ets, which constructs date data display for monthly or weekly views and enables date selection).
The code implementation of the calendar schedule item (ScheduleItem.ets) is described, covering functions such as displaying schedule information, formatting time, determining if a schedule has ended, and clicking to navigate to the schedule details page.

Basic Preparation

(1) Device Type: Huawei smartphone (bar phone)

(2) HarmonyOS Version: HarmonyOS 5.0.0 Release and above

(3) DevEco Studio Version: DevEco Studio 5.0.0 Release and above

(4) HarmonyOS SDK Version: HarmonyOS 5.0.0 Release SDK and above
## II. Application Overall Construction and Layout Design
Module Design

Schedule Module: ScheduleView.ets
### III. Implementation of Schedule Entry UI
Key code implementation in the pages>MainEntry.ets file under the products layer:

```arkts
// Import modules: bottom navigation configuration, functional module views, type definitions, etc.
import { TAB_LIST } from '../constants/Constants';
import { AgencyTaskView } from '@ohos_agcit/office_attendance_agency';
import { ScheduleView } from '@ohos_agcit/office_attendance_schedule';
import { TabListItem } from '@ohos_agcit/office_attendance_agency';
import { CheckInView } from '@ohos_agcit/office_attendance_checkin';
import { CommonConstants, MainEntryVM } from '@ohos_agcit/office_attendance_common_lib';
import { MineView } from '@ohos_agcit/office_attendance_account';
import { router } from '@kit.ArkUI';
 
// --------------------------
// Main entry page builder (for external calls)
// --------------------------
@Builder
export function mainEntryBuilder() {
  MainEntry();
}
 
@Entry // Mark as the application entry component
@ComponentV2 // Declare as an ArkUI V2 component
struct MainEntry {
  // Router management singleton
  vm: MainEntryVM = MainEntryVM.instance;
  
  // Page state control
  @Provider() isPageShow: boolean = false; // Whether the page is displayed
  @Provider() currentTabIndex: number = this.vm.curIndex; // Current selected tab index
  onPageShow(): void {
    this.isPageShow = true; // Update state when the page is displayed
  }
 
  onPageHide(): void {
    this.isPageShow = false; // Update state when the page is hidden
  }
 
  build() {
    Navigation(this.vm.navStack) { // Navigation container (manages page stack)
      // Bottom navigation bar configuration
      Tabs({ 
        barPosition: BarPosition.End, // Navigation bar position (bottom)
        index: this.vm.curIndex // Current selected index
      }) {
        // Tab1: Attendance punch-in module
        TabContent() {
          CheckInView(); // Load the punch-in component
        }
        .tabBar(this.tabBarBuilder(TAB_LIST[0], 0)); // Bind navigation bar style
 
        // Tab2: Pending tasks module
        TabContent() {
          AgencyTaskView(); // Load the pending tasks component
        }
        .tabBar(this.tabBarBuilder(TAB_LIST[1], 1));
 
        // Tab3: Schedule management module
        TabContent() {
          ScheduleView(); // Load the schedule component
        }
        .tabBar(this.tabBarBuilder(TAB_LIST[2], 2));
 
        // Tab4: Personal center module
        TabContent() {
          // MineView({ // Load the personal center component
          //   callback: () => { // Logout callback
          //     router.replaceUrl({ url: 'pages/Login' }) // Navigate to the login page
          //   }
          // });
        }
        .tabBar(this.tabBarBuilder(TAB_LIST[3], 3));
      }
      // Navigation bar style configuration
      .scrollable(false) // Disable swipe switching
      .barHeight(80) // Navigation bar height
      .height(CommonConstants.FULL_HEIGHT) // Full screen height
      .animationDuration(0) // Disable switching animation
      .barMode(BarMode.Fixed) // Fixed mode (does not scroll with content)
      .onChange((index: number) => { // Tab switching event
        this.vm.curIndex = index; // Update router management state
        this.currentTabIndex = index; // Update current index
      });
    }
    // Navigation bar title configuration
    .title({ 
      builder: this.titleBuilder(TAB_LIST[this.vm.curIndex]), // Dynamic title
      height: 92 // Title bar height
    })
    .mode(NavigationMode.Stack) // Stack navigation mode
  }
 
  @Builder
  titleBuilder(title: TabListItem) {
    Row() {
      Text(title.label) // Display the current module name
        .fontWeight(FontWeight.Bold) // Bold
        .fontColor('rgba(0,0,0,0.90)') // Font color
        .fontSize($r('app.float.navigation_title_font_size')) // Font size (resource reference)
        .margin({ left: 16, top: 36 }) // Margin
        .height(56) // Height
    }
    .justifyContent(FlexAlign.Start) // Left-aligned
    .backgroundColor(title.titleBackgroundColor) // Background color (dynamic configuration)
    .width('100%') // Full width
    .height('100%') // Full height
    .alignItems(VerticalAlign.Bottom) // Vertically bottom-aligned
  }
 
  // --------------------------
  // Bottom navigation bar item builder
  // --------------------------
  @Builder
  tabBarBuilder(item: TabListItem, index: number) {
    Column() { // Vertical layout
      // Dynamically display selected/unselected icons
      Image(this.vm.curIndex === index ? item.iconChecked : item.icon)
        .width($r('app.float.navigation_image_size')) // Icon width (resource reference)
        .height($r('app.float.navigation_image_size')) // Icon height
        .margin({ top: $r('app.string.margin_xs') }); // Top margin
      
      // Tab text label
      Text(item.label)
        .fontColor(this.vm.curIndex === index ? 
          $r('app.color.icon_color_highlight') : // Selected highlight color
          $r('app.color.icon_color_level2')) // Unselected default color
        .fontSize($r('app.float.navigation_navi_size')) // Font size
        .height(14) // Fixed height
        .margin({ 
          top: $r('app.string.margin_xs'), 
          bottom: $r('app.string.margin_xs') 
        });
    }
    .height(80) // Fixed height
    .width('100%') // Full width
    .justifyContent(FlexAlign.Start); // Top-aligned
  }
}
```
## IV. Schedule Function Module and Implementation Process
UI Structure Design of the Schedule Module

The schedule consists of the following core components:

Main Schedule View: Supports switching between monthly and weekly views, with highlighting of today's schedule.

Schedule Details Page: Displays time, location, participants, and reminder settings.

Add/Edit Page: Provides form input, map location selection, and attachment upload functionality.

Functional Implementation Steps

(1) Creation and Implementation Code of ScheduleView.ets File:

① Create a scenes directory under the root directory OfficeAttendance.

② Create a schedule module (HSP dynamic sharing) under the scenes directory.

③ Code Implementation of ScheduleView.ets:

```arkts
typescript
import { DataPreferencesUtils } from '@ohos_agcit/office_attendance_common_lib';
import { DateElement } from '@ohos_agcit/office_attendance_component_lib/src/main/ets/types/DateElement';
 
@ComponentV2
export struct ScheduleTitle {
  @Param selectedMonth: DateElement = new DateElement(0, 0, 0); // Currently selected month
  @Param onChange: (selected: DateElement) => void = (selected: DateElement) => {
  } // Callback function for month change
  @Param onLunarChange: () => void = () => {
  } // Callback function for lunar calendar display setting change
 
  // Switch to the previous month
  private goBeforeMonth() {
    this.selectedMonth.year =
      this.selectedMonth.month - 1 >= 0 ? this.selectedMonth.year : this.selectedMonth.year - 1;
    this.selectedMonth.month = this.selectedMonth.month - 1 >= 0 ? this.selectedMonth.month - 1 : 11;
    this.onChange(this.selectedMonth);
  }
 
  // Switch to the next month
  private goAfterMonth() {
    this.selectedMonth.year =
      this.selectedMonth.month + 1 < 12 ? this.selectedMonth.year : this.selectedMonth.year + 1;
    this.selectedMonth.month = this.selectedMonth.month + 1 < 12 ? this.selectedMonth.month + 1 : 0;
    this.onChange(this.selectedMonth);
  }
 
  // Check if lunar calendar display is enabled
  isSettingLunar() {
    return DataPreferencesUtils.LUNAR_SETTING_ON ===
    DataPreferencesUtils.getSync(DataPreferencesUtils.LUNAR_SETTING, DataPreferencesUtils.LUNAR_SETTING_ON)
  }
 
  @Builder
  settingBuilder() {
    Column() {
      Row() {
        Row() {
          Text($r('app.string.schedule_lc_desc')) // Display lunar calendar setting description text
        }.alignItems(VerticalAlign.Center)
        .justifyContent(FlexAlign.Start)
        .width('60%')
 
        Row() {
          // Display checkmark icon if lunar calendar display is enabled
          if (this.isSettingLunar()) {
            Image($r('app.media.ic_check_mask')).width(24).height(16)
          }
        }.alignItems(VerticalAlign.Center)
        .justifyContent(FlexAlign.End)
        .layoutWeight(1)
      }.alignItems(VerticalAlign.Center)
      .justifyContent(FlexAlign.Center)
      .onClick(() => {
        // Toggle lunar calendar display setting on click
        if (this.isSettingLunar()) {
          DataPreferencesUtils.putSync(DataPreferencesUtils.LUNAR_SETTING, DataPreferencesUtils.LUNAR_SETTING_OFF)
        } else {
          DataPreferencesUtils.putSync(DataPreferencesUtils.LUNAR_SETTING, DataPreferencesUtils.LUNAR_SETTING_ON)
        }
        this.onLunarChange();
      })
    }.height(45)
    .alignItems(HorizontalAlign.Center)
    .justifyContent(FlexAlign.Center)
    .width(240)
    .margin({ top: 10, bottom: 10 })
  }
 
  build() {
    Row() {
      // Left side: Month navigation area
      Row() {
        // Previous month button
        Row() {
          Image($r('app.media.ic_public_small_left'))
            .id('pre_month')
            .height(20)
            .width(12)
        }
        .margin({ left: 5 })
        .height('100%')
        .aspectRatio(1)
        .justifyContent(FlexAlign.Center)
        .onClick(() => {
          this.goBeforeMonth();
        })
        Column().width('6%') // Spacing
        // Display current year and month
        Text(`${this.selectedMonth.year}-${this.selectedMonth.month + 1 >= 10 ? '' : '0'}${this.selectedMonth.month +
          1}`)
          .fontSize(16)
          .fontWeight(FontWeight.Medium)
          .opacity(0.9)
        Column().width('6%') // Spacing
        // Next month button
        Row() {
          Image($r('app.media.ic_public_small_right'))
            .id('next_month')
            .height(20)
            .width(12)
        }
        .margin({ left: 5 })
        .height('100%')
        .aspectRatio(1)
        .justifyContent(FlexAlign.Center)
        .onClick(() => {
          this.goAfterMonth();
        })
        .alignItems(VerticalAlign.Center)
        .justifyContent(FlexAlign.Start)
      }.width('50%')
      .alignItems(VerticalAlign.Center)
      .justifyContent(FlexAlign.Start)
 
      // Right side: Settings button
      Row() {
        Image($r('app.media.ic_ellipsis'))
          .width(20)
          .height(12)
          .bindMenu(this.settingBuilder) // Bind settings menu
      }
      .width('50%')
      .alignItems(VerticalAlign.Center)
      .justifyContent(FlexAlign.End)
    }.width('100%')
    .height(24)
  }
}
 
// Description: Calendar - Date
// Implementation of CalenderView.ets
import { i18n } from '@kit.LocalizationKit';
import { CommonConstants, DateUtils } from '@ohos_agcit/office_attendance_common_lib';
import { DateElement } from '../types/DateElement';
 
@ObservedV2
class ShowDays {
  @Trace showDays: DateElement [][] = [];  // Monthly view date data (2D array)
  @Trace showWeekDays: DateElement [][] = [];  // Weekly view date data
}
 
@ComponentV2
export struct CalenderView {
  // Component parameters
  @Param isShowLunar: boolean = true;  // Whether to display lunar calendar
  @Param fontSize: Resource = $r('app.float.calendar_title_font_size')  // Font size resource
  @Param titleFontOpacity: Resource = $r('app.float.calendar_title_font_opacity')  // Title opacity
  @Param contentFontOpacity: Resource = $r('app.float.calendar_content_font_opacity')  // Content opacity
  @Param weekendFontOpacity: Resource = $r('app.float.calendar_weekend_font_opacity')  // Weekend opacity
  @Param beforeDateFontOpacity: Resource = $r('app.float.calendar_before_date_font_opacity')  // Non-current month date opacity
  @Param todayFontOpacity: Resource = $r('app.float.calendar_today_font_opacity')  // Today's opacity
  readonly TIME_DEF_VALUE = -1;  // Default time value
 
  // Localized calendar instance
  private calendar = i18n.getCalendar(CommonConstants.EN_LOCALE);
  @Local today: number = this.calendar.get(CommonConstants.FIELD_DAY);  // Current day
  @Local currentMonth: number = this.calendar.get(CommonConstants.FIELD_MONTH);  // Current month
  @Local currentYear: number = this.calendar.get(CommonConstants.FIELD_YEAR);  // Current year
 
  // Component state
  @Param selectedDay: DateElement = new DateElement(0, 0, this.TIME_DEF_VALUE);  // Selected date
  @Event onChangeDay: (val: DateElement) => void = (val: DateElement) => {};  // Date change event
  @Param showMonth: DateElement = new DateElement(this.TIME_DEF_VALUE, this.TIME_DEF_VALUE, this.TIME_DEF_VALUE);  // Displayed month
  @Event onChangeMonth: (showMonth: DateElement) => void = (showMonth: DateElement) => {};  // Month change event
  @Param showDotDate: Array<string> = []  // Dates to display dots
  @Local viewType: number = CommonConstants.MONTH_VIEW;  // View type (month/week)
  @Event onChangeViewType: (showType: number) => void = (showType: number) => {};  // View switch event
  @Param hasScheduleDates: Array<string> = [];  // List of dates with schedules
  showDays: ShowDays = new ShowDays();  // Date data instance
 
  // Monitor month and selected day changes
  @Monitor('showMonth','selectedDay')
  onSelectedMonthChange(monitor: IMonitor) {
    this.refresh();
  }
 
  // Check if it's a weekend
  isWeekends(index: number) {
    return index === CommonConstants.COMMON_ZERO || index === CommonConstants.COMMON_SIX;
  }
 
  // Refresh calendar data
  refresh() {
    this.init();
    // Set calendar to the first day of the displayed month
    this.calendar.set(this.showMonth.year, this.showMonth.month, 0);
    
    // Calculate previous and next month information
    let preMonth = this.showMonth.month - 1 >= 0 ? this.showMonth.month - 1 : 11;
    let preMonthYear = this.showMonth.month - 1 >= 0 ? this.showMonth.year : this.showMonth.year - 1;
    let preMonthDays: number = -this.calendar.compareDays(new Date(preMonthYear, preMonth, 0)) - 1;
    
    let nextMonth = this.showMonth.month + 1 < 12 ? this.showMonth.month + 1 : 0;
    let nextMonthYear = this.showMonth.month + 1 < 12 ? this.showMonth.year : this.showMonth.year + 1;
    let selectMonthDays: number = this.calendar.compareDays(new Date(nextMonthYear, nextMonth, 0));
    
    // Build date array
    let firstDayOfWeek = this.calendar.get(CommonConstants.FIELD_DAY_OF_WEEK);
    let weedDays: DateElement [] = [];
    let diffDays = firstDayOfWeek < 7 ? firstDayOfWeek : 0;
 
    // Fill previous month's dates
    for (let i = 1; i <= diffDays; i++) {
      weedDays.push(new DateElement(preMonthYear, preMonth, preMonthDays - diffDays + i));
    }
    
    // Fill current month's dates
    for (let i = 1; i <= selectMonthDays; i++) {
      if (weedDays.length === 7) {
        this.showDays.showDays.push(weedDays);
        weedDays = [];
      }
      weedDays.push(new DateElement(this.showMonth.year, this.showMonth.month, i));
    }
    
    // Fill next month's dates
    if (weedDays.length < 7) {
      let diffDays = 7 - weedDays.length;
      for (let i = 0; i < diffDays; i++) {
        weedDays.push(new DateElement(nextMonthYear, nextMonth, i + 1));
      }
    }
    this.showDays.showDays.push(weedDays);
 
    // Synchronize selected date state
    if (this.selectedDay.year !== this.showMonth.year || this.selectedDay.month !== this.showMonth.month) {
      this.selectedDay.year = this.showMonth.year;
      this.selectedDay.month = this.showMonth.month;
      this.onChangeDay(this.selectedDay);
    }
    if (this.selectedDay.day === this.TIME_DEF_VALUE) {
      this.selectedDay.day = this.today;
      this.onChangeDay(this.selectedDay);
    }
    this.setWeedViewData()
  }
 
  // Set weekly view data
  setWeedViewData() {
    this.showDays.showDays.forEach(week => {
      week.forEach(day => {
        if (this.isSelectedDay(day)) {
          this.showDays.showWeekDays = [week];  // Find the week containing the selected day
        }
      })
    })
  }
 
  // Initialize month
  init() {
    this.showDays.showDays = [];
    if (this.showMonth.year === this.TIME_DEF_VALUE) {
      this.showMonth.year = this.currentYear;
      this.showMonth.month = this.currentMonth;
      this.onChangeMonth(this.showMonth);
    }
  }
 
  // Lifecycle: Component is about to appear
  aboutToAppear() {
    this.refresh();
  }
 
  // Check if it's today
  private isToday(day: DateElement): boolean {
    return this.today === day.day && this.currentMonth === day.month &&
      this.currentYear === day.year;
  }
 
  // Check if it's a past date
  isBeforeDay(day: DateElement) {
    return this.calendar.compareDays(new Date(day.year, day.month, day.day)) < 0;
  }
 
  // Zero-padding formatting
  appendZero(num: number): string {
    return num < 10 ? '0' + num : '' + num;
  }
 
  // Check if it's the selected day
  private isSelectedDay(day: DateElement): boolean {
    return this.selectedDay.isEquals(day);
  }
 
  // Check if it's the current month
  isCurrentMonth(day: DateElement) {
    return this.showMonth.year === day.year && this.showMonth.month === day.month;
  }
 
  // Switch month/week by gesture
  private changeMonthByGesture(e: GestureEvent) {
    if (e.offsetX > 10) {  // Swipe left
      if (this.viewType === CommonConstants.MONTH_VIEW) {
        // Switch to previous month in monthly view
        this.showMonth.year = this.showMonth.month - 1 < 0 ? this.showMonth.year - 1 : this.showMonth.year;
        this.showMonth.month = this.showMonth.month - 1 < 0 ? 11 : this.showMonth.month - 1;
        this.onChangeMonth(this.showMonth);
      } else {
        // Switch to previous week in weekly view
        let selectedDate = new Date(this.selectedDay.year, this.selectedDay.month, this.selectedDay.day);
        selectedDate = new Date(selectedDate.getTime() - CommonConstants.SEVEN_DAY);
        this.changeSelectDay(new DateElement(selectedDate.getFullYear(),
          selectedDate.getMonth(),
          selectedDate.getDate()))
      }
    } else if (e.offsetX < -10) {  // Swipe right
      if (this.viewType === CommonConstants.MONTH_VIEW) {
        // Switch to next month in monthly view
        this.showMonth.year = this.showMonth.month + 1 < 12 ? this.showMonth.year : this.showMonth.year + 1;
        this.showMonth.month = this.showMonth.month + 1 < 12 ? this.showMonth.month + 1 : 0;
        this.onChangeMonth(this.showMonth);
      } else {
        // Switch to next week in weekly view
        let selectedDate = new Date(this.selectedDay.year, this.selectedDay.month, this.selectedDay.day);
        selectedDate = new Date(selectedDate.getTime() + CommonConstants.SEVEN_DAY);
        this.changeSelectDay(new DateElement(selectedDate.getFullYear(),
          selectedDate.getMonth(),
          selectedDate.getDate()))
      }
    }
  }
 
  // Change selected day
  changeSelectDay(day: DateElement) {
    this.selectedDay.year = day.year;
    this.selectedDay.month = day.month;
    this.selectedDay.day = day.day;
    this.onChangeDay(day);
    this.setWeedViewData();
    this.changeMonth(day);
  }
 
  // Switch month
  changeMonth(month: DateElement) {
    this.showMonth.year = month.year;
    this.showMonth.month = month.month;
    this.onChangeMonth(month);
  }
 
  // Check if there's a schedule
  isHasSchedule(day: DateElement) {
    let dayStr: string = day.getDateString();
    return this.hasScheduleDates.filter(t => {
      return t.indexOf(dayStr) > -1;
    }).length > 0;
  }
 
  build() {
    Column() {
      // Week title area
      Column() {
        Flex({ justifyContent: FlexAlign.SpaceBetween }) {
          ForEach(CommonConstants.WEEK_TITLE, (str: Resource, index: number) => {
            Column() {
              Text(str)  // Display weekday title
                .fontSize(this.fontSize)
                .fontWeight(FontWeight.Regular)
                .textAlign(TextAlign.Center)
                .opacity(this.titleFontOpacity)
            }
            .justifyContent(FlexAlign.Center)
            .width($r('app.float.content_size'))
            .height($r('app.float.height_or_line_height'))
          })
        }
        .width(CommonConstants.FULL_PERCENT)
        .borderRadius($r('app.float.height_or_line_height'))
        .margin({
          bottom: $r('app.float.day_button_radius'),
          left: CommonConstants.COMMON_MARGIN,
          right: CommonConstants.COMMON_MARGIN
        })
      }
 
      // Date body area
      Flex({ wrap: FlexWrap.Wrap }) {
        ForEach(this.viewType === CommonConstants.MONTH_VIEW ? this.showDays.showDays : this.showDays.showWeekDays,
          (weekDays: DateElement []) => {
            Flex({ justifyContent: FlexAlign.SpaceBetween }) {
              ForEach(weekDays, (day: DateElement, index: number) => {
                Column() {
                  // Solar date
                  Text(`${day.day}`)
                    .fontSize(this.fontSize)
                    .fontWeight(FontWeight.Medium)
                    .opacity(this.isToday(day) ? this.todayFontOpacity :  // Today's opacity
                      this.isWeekends(index) ? this.weekendFontOpacity :  // Weekend opacity
                        this.isCurrentMonth(day) ? this.contentFontOpacity : this.beforeDateFontOpacity)  // Non-current month opacity
                    .fontColor(this.isToday(day) && !this.isSelectedDay(day) ? $r('app.color.start_window_background') :
                    $r('app.color.black'))
                  
                  // Lunar date
                  Text(DateUtils.getCurrentLunarDayDesc(day.year, day.month, day.day))
                    .fontSize(this.fontSize)
                    .visibility(this.isShowLunar ? Visibility.Visible : Visibility.None)
                    .opacity(this.isToday(day) ? this.todayFontOpacity :  // Opacity settings same as above
                      this.isWeekends(index) ? this.weekendFontOpacity :
                        this.isCurrentMonth(day) ? this.contentFontOpacity : this.beforeDateFontOpacity)
                    .fontColor(this.isToday(day) && !this.isSelectedDay(day) ? $r('app.color.start_window_background') :
                    $r('app.color.black'))
 
                  // Schedule dot marker
                  Column()
                    .width(4)
                    .height(4)
                    .borderRadius(2)
                    .backgroundColor(this.isSelectedDay(day) ? $r('app.color.sys_default_blue') :  // Selected state color
                      this.isToday(day) ? Color.White : $r('app.color.sys_default_blue'))  // Today's color
                    .visibility(this.isHasSchedule(day) ? Visibility.Visible : Visibility.None)  // Display when there's a schedule
                }
                .onClick(() => {
                  this.changeSelectDay(day);  // Select date on click
                })
                // Background style
                .backgroundColor(this.isToday(day) && !this.isSelectedDay(day) ? $r('app.color.sys_default_blue') :
                Color.White)
                .borderRadius($r('app.float.schedule_border_radius'))
                .borderColor(this.isSelectedDay(day) ? $r('app.color.sys_default_blue') : Color.White)  // Selected border
                .borderWidth(this.isSelectedDay(day) ? 1 : 0)
                .justifyContent(FlexAlign.Center)
                .width($r('app.float.content_size'))
                .height($r('app.float.content_size'))
              })
            }
            .width('100%')
            .margin({ top: '6vp' })
          })
      }
      .width('100%')
      // Add horizontal swipe gesture
      .gesture(PanGesture({ fingers: 1, direction: PanDirection.Horizontal, distance: 5 })
        .onActionEnd((e: GestureEvent) => {
          this.changeMonthByGesture(e);  // Switch month/week by gesture
        })
      )
 
      Column().height('2%')  // Spacing area
 
      // View switch buttons
      Column() {
        // Expand monthly view button
        Image($r('app.media.ic_chevron_up'))
          .width(64)
          .height(16)
          .visibility(this.viewType === CommonConstants.MONTH_VIEW ? Visibility.Visible : Visibility.None)
          .onClick(() => {
            this.viewType = CommonConstants.WEEK_VIEW
            this.onChangeViewType(this.viewType)
          })
        // Collapse weekly view button  
        Image($r('app.media.ic_chevron_down'))
          .width(64)
          .height(16)
          .visibility(this.viewType === CommonConstants.WEEK_VIEW ? Visibility.Visible : Visibility.None)
          .onClick(() => {
            this.viewType = CommonConstants.MONTH_VIEW
            this.onChangeViewType(this.viewType)
          })
      }.height(16)
      .width('100%')
    }
  }
}
```
