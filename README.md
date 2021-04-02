# Pull Request Test

<h3>이 프로그램은 왼쪽 마우스 사용자를 위한 Autohotkey 프로그램입니다.</h3>

Autohotkey 에 대한 자세한 정보는 <link>https://autohotkey.com/</link> 에서 확인하실 수 있습니다.
프로그램은 <link>https://autohotkey.com/download/ahk-install.exe</link>에서 다운로드 받을 수 있습니다.

이 프로그램을 지속적으로 사용하기 위해서는 윈도우 시작시에 자동으로 실행을 할 수 있도록 시작프로그램에 등록해야 합니다.<br />
아래의 레지스트리를 이용하여 프로그램을 등록할 수 있습니다.

<pre>
REGEDIT4

[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run]
"HomeHotKey"="c:\\documents\\leftmouseutil.ahk"
</pre>

- autohotkey 프로그램은 확장자로 .ahk를 사용합니다.
  그러므로 아래의 코드를 다운로드 받아 컴퓨터에 적당한 폴더(예: c:\documents)에 저장할 때 leftmouse.ahk 라고 저장을 합니다.
  위의 레지스트리를 이용하여 등록하려면 c:\documents\leftmouse.ahk 라고 저장합니다.
- 주석은 ; (semicolon) 으로 시작합니다. 그러므로 맘에 안 드는 부분이 있으면 ; 으로 주석처리하면 됩니다.

<pre>
#SingleInstance force
#Persistent ; DO NOT QUIT

;
; Modified on 18 July, 2020
; 단축키는 아래와 같습니다.
; Windows (#), Ctrl (^), Alt (!), Shift (+)
;

DetectHiddenWindows On
SendMode Input  ; Recommended for new scripts due to its superior speed and reliability.
SetWorkingDir %A_ScriptDir%  ; Ensures a consistent starting directory.
SetTitleMatchMode, RegEx
AutoTrim, on
CoordMode, ToolTip, Relative
EnvGet ProgramW6432, ProgramW6432
Menu, Tray, Icon, ahk.ico
global OSVER := SubStr(A_OSVersion, 1, 4)
global ProgramFilesX86 := A_ProgramFiles . (A_PtrSize = 8 ? " (x86)" : "")
global DefaultPath := (ProgramW6432 ? ProgramW6432 : A_ProgramFiles) "\AutoHotkey"
global CHROME_CMD := "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe"

; 우측 Alt 키는 한영 전환, 우측 Ctrl 키는 한자키
; [단축키] -> 해당 기능 설명입니다.
; LeftShift 키와 RightShift 키는 구분하여 실행될 수 있으므로 만약 LeftShift 라고 선언되어 있는 경우엔
; LeftShift 키를 누르셔야 합니다.
; 구분이 없는 경우엔 아무키나 눌러도 무방합니다.

; #EscapeChar \
; [Window + Space] -> 항상 윈도우 상단 표시 [토글]
#SPACE::Winset, Alwaysontop, , A
; [LeftShift + Space] -> 한영 전환
<+space::SendInput, {vk15sc138}
*RWin::AppsKey
SC138 UP::SendInput, {vk15sc1f2}
+SC11D UP::SendInput, {vk19sc1f1}
; [RightWindow + \] -> 창 전환하는 Atl + tab
RWin & \::AltTab
; [RightWindow + BackSpace] -> 창 역순 전환하는 Alt + Shift + tab
RWin & BackSpace::ShiftAltTab
; [RightAlt + \] -> 창 전환하는 Atl + tab
SC138 & \::AltTab
; [RightAlt + BackSpace] -> 창 역순 전환하는 Alt + Shift + tab
SC138 & BackSpace::ShiftAltTab
; 이하는 오른쪽 Ctrl 키를 활성화
; [RightCtrl + BackSpace] -> Ctrl + BackSpace
SC11D & BackSpace::SendInput, ^{BackSpace}
; [RightCtrl + c] -> copy (ctrl + c)
SC11D & Ins::SendInput, ^c
; [RightCtrl + del] -> ctrl + del
SC11D & Del::SendInput, ^{Del}
SC11D & Home::SendInput, ^{Home}
SC11D & End::SendInput, ^{End}
SC11D & PgUp::SendInput, ^{PgUp}
SC11D & PgDn::SendInput, ^{PgDn}
SC11D & Left::SendInput, ^{Left}
SC11D & Right::SendInput, ^{Right}
SC11D & Up::SendInput, ^{Up}
SC11D & Down::SendInput, ^{Down}
; [RightCtrl + =] -> ctrl + = (인터넷 브라우저인 경우 확대)
SC11D & =::SendInput, ^=
; [RightCtrl + -] -> ctrl + - (인터넷 브라우저인 경우 축소)
SC11D & -::SendInput, ^-
; [RightCtrl + [] -> ctrl + shift + tab (크롬의 경우 역순 탭 전환)
SC11D & [::SendInput, ^+{Tab}
; [RightCtrl + ]] -> ctrl + tab (크롬의 경우 순차 탭 전환)
SC11D & ]::SendInput, ^{Tab}
; [RightCtrl + \] -> Esc
SC11D & \::SendInput, {Esc}
; [RightCtrl + ;] -> Ctrl + f (찾기)
SC11D & `;::SendInput, ^f
SC11D & ,::SendInput, ^,
SC11D & .::SendInput, ^.
SC11D & /::SendInput, ^/
RWin & S::RunAuSpy()

; Logoff: 0, Shutdown: 1, Reboot 2, Force: 4, Power Down: 8
#^Del::WinShutdown(8)
#^BackSpace::WinShutdown(0)
#ScrollLock::DllCall("PowrProf\SetSuspendState", "int", 0, "int", 0, "int", 0)
~#Tab::ViewVirtualDesktop()
; $#^Del::FileRecycleEmpty ; Win+Del to empty trash (recycle bin)
>+Space::SendInput, {PgUp}
^+Space::SendInput, {PgUp}
!<::SendInput, ^{Home}
!>::SendInput, ^{End}
!i::MouseClickVoid()
!p::SendInput, {Up}
!n::SendInput, {Down}
!+p::MouseMoveThruKbd(0, -14)
!+n::MouseMoveThruKbd(0, 14)
!+j::MouseMoveThruKbd(0, 14)
!+k::MouseMoveThruKbd(0, -14)
!+h::MouseMoveThruKbd(-14, 0)
!+l::MouseMoveThruKbd(14, 0)

; 문서 작성시에 _dt 라고 입력을 하고 SpaceBar 를 누르면 오늘의 날짜로 변환
::_dt::
FormatTime, TimeString, , yyyy-MM-dd
SendInput %TimeString%
return

; 문서 작성시에 ytd 라고 입력을 하고 SpaceBar 를 누르면 자동으로 오늘의 날짜로 변환
::ytd::
ytd =
ytd += -1, days
FormatTime, TimeString, %ytd%, yyyy-MM-dd
SendInput %TimeString%
return

; [Global] Window + tab
ViewVirtualDesktop()
{
    Sleep, 500
    SendInput, {AppsKey}m
    return
}

; [Global] ctrl + ,
WhichDesktop(num)
{
    SendInput, {Left}{Esc}
    Sleep, 100
    if (num == -1) {
        SendInput, {Left}
    } else {
        SendInput, {Right}
    }
    Sleep, 200
    SendInput, {AppsKey}
    Sleep, 100
    SendInput, m
    return
}

; [global] Windows Desktop Manager
#IfWinActive, ahk_class MultitaskingViewFrame
^,::WhichDesktop(-1)
^.::WhichDesktop(1)
q::SendInput, {AppsKey}m
d::SendInput, #^d
#IfWinActive

; [Global] ctrl + alt + shift + s
RunAuSpy()
{
    Run, %DefaultPath%\AU3_Spy.exe
    Sleep, 500
    WinMove, Active Window Info, , A_ScreenWidth/2 + 460, A_ScreenHeight/2 - 520 ; High DPI
    ; WinMove, Active Window Info, , A_ScreenWidth/2 + 520, A_ScreenHeight/2 - 80  ; 1920 x 1280
    Return
}

; [Global] Windows + ctrl + del
; Logoff: 0, Shutdown: 1, Reboot 2, Force: 4, Power Down: 8
WinShutdown(code, stime = 5000)
{
    SleepDefault(stime)
    Shutdown, code
}

GetProgramPath(forced := False)
{
    return forced ? ProgramFilesX86 : (ProgramW6432 ? ProgramW6432 : A_ProgramFiles)
    ; return (A_PtrSize = 8 ? ProgramFilesX86 : A_ProgramFiles)
}

SwitchVirWin(direction)
{
    If (OSVER = "10.0")
    {
        SendInput, #^{%direction%}
        Sleep, 500
    }
}

KillProgram(name, stime = 100)
{
    Process, Close, %name%
    SleepDefault(stime)
}

SleepDefault(stime = 500)
{
    If (stime > 0)
    {
        Sleep, %stime%
    }
}

SendCmd(cmd, stime = 500)
{
    SendInput, %cmd%
    SleepDefault(stime)
}

SendCmdFocus(cmd, focus = "{Enter}", stime = 500)
{
    SendCmd(cmd, stime)
    SendInput, %focus%
}

MouseClickThruKbd(whichBtn="Left", stime = 500)
{
    ; MouseGetPos, x, y
    ; MouseClick, %whichBtn%, x, y
    MouseClick, %whichBtn%, , , , , , R
    SleepDefault(stime)
}

MouseClickVoid(key = "{Alt Up}", whichBtn="Left", stime = 500)
{
    ; MouseGetPos, x, y
    SendEvent, {Blind}%key%
    MouseClick, %whichBtn%, , , , , , R
    SleepDefault(stime)
}

MouseMoveThruKbd(x = 0, y = 0)
{
    MouseMove, x, y, 0, R
}

MouseClickApp(x, y, whichBtn = "Left", stime = 500, cnt = 1)
{
    MouseClick, %whichBtn%, x, y, %cnt%
    SleepDefault(stime)
}

MouseMoveApp(x, y)
{
    MouseMove, x, y, , R
}

MouseClickIE(x, y)
{
    if WinExist(" - Internet Explorer")
        MouseClick, Left, x, y
}

MouseClickAddIE(a, b)
{
    if WinExist("- Internet Explorer")
    {
        MouseGetPos, x, y
        MouseClick, Left, x + a, y + b
    }
}

MouseMoveIE(x, y)
{
    MouseMoveApp(x, y)
}

RunProgram(name, cmd, title = "ahk_exe")
{
    b := 0
    Process, Exist, %name%
    wTitle := title
    if (title = "ahk_exe")
    {
        wTitle := title " " name
    }
    else
    {
        wTitle := title
    }

    if (ErrorLevel = 0)
    {
        Run, %cmd%
        WinWait, %wTitle%, , 3
    }
    else
    {
        WinActivate, %wTitle%
    }
    b := 1
    return b
}

RunIE(url)
{
    IfWinExist, ahk_class IEFrame
    {
        WinActivate
    }
    Else
    {
        Pwb := ComObjCreate("InternetExplorer.Application")
        Pwb.Visible := True
        IME_EN()
        Pwb.Navigate(url)
        SleepDefault(1000)
    }
    Return
}

; [chrome]
NavThruBrowser(url, stime = 2000)
{
    SendCmd("^t", 100)
    IME_EN()
    SendCmd("!dhttp:`/`/" . url . "{enter}", stime)
}

; [chrome] alt + s
SaveImage()
{
    MouseClickThruKbd("Right")
    SendCmd("v")
}

; [chome] ctrl + alt + c
DupCurrentTab()
{
    SendCmdFocus("!d", "^c^t")
    SendCmd("^v{Enter}")
}

; [chrome] alt + k
CopyLink()
{
    MouseClickThruKbd("Right")
    SendCmd("e")
}

; [chrome] alt + h
GoHome()
{
    SendCmd("^{Home}")
    SendCmdFocus("f", "aa")
}

; [chrome] alt + f1
GoTranslatePage()
{
    SendCmd("^c")
    NavThruBrowser("translate.google.com/?hl=ko")
    Sleep, 800
    SendCmd("^v")
}

; [chrome] alt + f3
SearchWithText()
{
    SendCmd("^c{F3}")
    SendCmdFocus("^v", "{Enter}")
}

; [chrome] alt + shift + a
OpenChromeApp()
{
    SendCmd("^t")
    MouseClickApp(30, 80, 2000)
    SendCmd("{Tab}")
}

; [chrome] alt + shift + c
SearchWithGoolge()
{
    clipSaved := clipboard
    SendCmdFocus("^c", "^t")
    Sleep, 100
    SendCmdFocus("https://www.google.co.uk/search?q=" clipboard, "{Enter}")
    clipboard := clipSaved
    clipSaved =
}

; shortcut for Chrome
#IfWinActive, ahk_class Chrome_WidgetWin_1
!+g::SearchWithGoolge()
!+o::MouseClickAddIE(0, 0)
!+a::OpenChromeApp()
!f1::GoTranslatePage()
!f3::SearchWithText()
^!c::DupCurrentTab()
!k::CopyLink()
!s::SaveImage()
!h::GoHome()
#IfWinActive

notify(text, stime = 1500)
{
    if (text = "") {
        SplashTextOff
        return
    }

    SplashTextOn, , , %text%
    WinMove, %text%, , A_ScreenWidth - 365, A_ScreenHeight - 70 ; High DPI
    ; WinMove, %text%, , A_ScreenWidth - 280, A_ScreenHeight - 34 ; High DPI
    Sleep, %stime%
    SplashTextOff
    return
}

UninstallProgram()
{
    SendCmd("+{f10}")
    SendCmdFocus("u", "{Tab}", 3000)
}

;
; appwiz.cpl
;
#IfWinActive, ahk_class CabinetWClass
^+Del::UninstallProgram()
#IfWinActive

RepeatCmd(rpt, cmd, stime = 0)
{
    Loop, %rpt%
    {
        SendCmd(cmd, stime)
    }
}

OpenWithFile(FILE, stime = 1000)
{
    SendCmdFocus("^o", FILE . "{Enter}", stime)
}

;
; IME
;
IME_KO()
{
    if (IME_CHECK() = "0")
        Send, {vk15sc138}
    notify("KO")
    Sleep, 200
}

IME_EN()
{
    if (IME_CHECK() <> "0")
        Send, {vk15sc138}
    notify("EN")
}

IME_CHECK(WinTitle = "A")
{
    WinGet, hWnd, ID, %WinTitle%
    Return Send_ImeControl(ImmGetDefaultIMEWnd(hWnd), 0x005, "")
}

Send_ImeControl(DefaultIMEWnd, wParam, lParam)
{
    DetectSave := A_DetectHiddenWindows
    DetectHiddenWindows, ON
    SendMessage 0x283, wParam, lParam, , ahk_id %DefaultIMEWnd%
    if (DetectSave <> A_DetectHiddenWindows)
        DetectHiddenWindows, %DetectSave%
    return ErrorLevel
}

ImmGetDefaultIMEWnd(hWnd)
{
    return DllCall("imm32\ImmGetDefaultIMEWnd", Uint, hWnd, Uint)
}

;
; CapsLock
;
CapsLock & Esc::SendInput, {Blind}^{Esc}

Capslock & a::SendInput, {Blind}^a
Capslock & a up::SendInput, {Blind}
Capslock & b::SendInput {Blind}^b
Capslock & b up::SendInput {Blind}
Capslock & c::SendInput, {Blind}^c
Capslock & c up::SendInput, {Blind}
Capslock & d::SendInput, {Blind}^d
Capslock & d up::SendInput, {Blind}
Capslock & e::SendInput, {Blind}{End DownTemp}
Capslock & e up::SendInput, {Blind}{End Up}

Capslock & f::SendInput {Blind}^f
Capslock & f up::SendInput {Blind}
Capslock & g::SendInput {Blind}^g
Capslock & g up::SendInput {Blind}
Capslock & h::SendInput, {Blind}^h
Capslock & h up::SendInput, {Blind}
Capslock & i::SendInput {Blind}^i
Capslock & i up::SendInput {Blind}
Capslock & j::SendInput, {Blind}{Down DownTemp}
Capslock & j up::SendInput, {Blind}{Down Up}

Capslock & k::SendInput, {Blind}{Up DownTemp}
Capslock & k up::SendInput, {Blind}{Up Up}
Capslock & l::SendInput, {Blind}^l
Capslock & l up::SendInput, {Blind}
Capslock & m::SendInput, {Blind}^m
Capslock & m up::SendInput, {Blind}
Capslock & n::SendInput, {Blind}^n
Capslock & n up::SendInput, {Blind}
Capslock & o::SendInput, {Blind}^o
Capslock & o up::SendInput, {Blind}

Capslock & p::SendInput, {Blind}^p
Capslock & p up::SendInput, {Blind}
Capslock & q::SendInput, {Blind}^q
Capslock & q up::SendInput, {Blind}
Capslock & r::SendInput, {Blind}^r
Capslock & r up::SendInput, {Blind}
Capslock & s::SendInput, {Blind}^s
Capslock & s up::SendInput, {Blind}
Capslock & t::SendInput, {Blind}^t
Capslock & t up::SendInput, {Blind}

Capslock & u::SendInput, {Blind}^u
Capslock & u up::SendInput, {Blind}
Capslock & v::SendInput, {Blind}^v
Capslock & v up::SendInput, {Blind}
Capslock & w::SendInput, {Blind}^w
Capslock & w up::SendInput, {Blind}
Capslock & x::SendInput, {Blind}^x
Capslock & x up::SendInput, {Blind}
Capslock & y::SendInput, {Blind}^y
Capslock & y up::SendInput, {Blind}

Capslock & z::SendInput, {Blind}^z
Capslock & z up::SendInput, {Blind}

; CapsLock & 1::SendInput, {Blind}^1
; CapsLock & 1 Up::SendInput {Blind}
; CapsLock & 2::SendInput, {Blind}^2
; CapsLock & 2 Up::SendInput {Blind}
; CapsLock & 3::SendInput, {Blind}^3
; CapsLock & 3 Up::SendInput {Blind}
; CapsLock & 4::SendInput, {Blind}^4
; CapsLock & 4 Up::SendInput {Blind}
; CapsLock & 5::SendInput, {Blind}^5
; CapsLock & 5 Up::SendInput {Blind}
; CapsLock & 6::SendInput, {Blind}^6
; CapsLock & 6 Up::SendInput {Blind}
; CapsLock & 7::SendInput, {Blind}^7
; CapsLock & 7 Up::SendInput {Blind}
; CapsLock & 8::SendInput, {Blind}^8
; CapsLock & 8 Up::SendInput {Blind}
; CapsLock & 9::SendInput, {Blind}^9
; CapsLock & 9 Up::SendInput {Blind}
; CapsLock & 0::SendInput, {Blind}^0
CapsLock & 0 Up::SendInput {Blind}

CapsLock & f1::SendInput, {Blind}^{f1}
CapsLock & f1 Up::SendInput, {Blind}
CapsLock & f2::SendInput, {Blind}^{f2}
CapsLock & f2 Up::SendInput, {Blind}
CapsLock & f3::SendInput, {Blind}^{f3}
CapsLock & f3 Up::SendInput, {Blind}
CapsLock & f4::SendInput, {Blind}^{f4}
CapsLock & f4 Up::SendInput, {Blind}
CapsLock & f5::SendInput, {Blind}^{f5}
CapsLock & f5 Up::SendInput, {Blind}
CapsLock & f6::SendInput, {Blind}^{f6}
CapsLock & f6 Up::SendInput, {Blind}
CapsLock & f7::SendInput, {Blind}^{f7}
CapsLock & f7 Up::SendInput, {Blind}
CapsLock & f8::SendInput, {Blind}^{f8}
CapsLock & f8 Up::SendInput, {Blind}
CapsLock & f9::SendInput, {Blind}^{f9}
CapsLock & f9 Up::SendInput, {Blind}
CapsLock & f10::SendInput, {Blind}^{f9}
CapsLock & f10 Up::SendInput, {Blind}
CapsLock & f11::SendInput, {Blind}^{f11}
CapsLock & f11 Up::SendInput, {Blind}
CapsLock & f12::SendInput, {Blind}^{f12}
CapsLock & f12 Up::SendInput, {Blind}

CapsLock & -::SendInput, {Blind}^-
CapsLock & - Up::SendInput, {Blind}
CapsLock & +::SendInput, {Blind}^+
CapsLock & + Up::SendInput, {Blind}

Capslock & space::SendInput, {Blind}{PgDn DownTemp}
Capslock & space Up::SendInput, {Blind}

Capslock & Left::SendInput, {Blind}^{Left DownTemp}
Capslock & Left Up::SendInput, {Blind}{Left Up}
Capslock & Right::SendInput {Blind}^{Right Down}
Capslock & Right Up::SendInput {Blind}{Right Up}

Capslock & Up::SendInput, {Blind}^{Up DownTemp}
Capslock & Up Up::SendInput, {Blind}{Up Up}
Capslock & Down::SendInput {Blind}^{Down DownTemp}
Capslock & Down Up::SendInput {Blind}{Down Up}

Capslock & Ins::SendInput, {Blind}^{Ins DownTemp}
Capslock & Ins Up::SendInput, {Blind}{Ins Up}
Capslock & Del::SendInput, {Blind}^{Del DownTemp}
Capslock & Del Up::SendInput, {Blind}{Del Up}
Capslock & Home::SendInput, {Blind}^{Home DownTemp}
Capslock & Home Up::SendInput, {Blind}{Home Up}
Capslock & End::SendInput, {Blind}^{End DownTemp}
Capslock & End Up::SendInput, {Blind}{End Up}
Capslock & PgUp::SendInput, {Blind}^{PgUp DownTemp}
Capslock & PgUp Up::SendInput, {Blind}{PgUp Up}
Capslock & PgDn::SendInput {Blind}^{PgDn DownTemp}
Capslock & PgDn Up::SendInput {Blind}{PgDn Up}

CapsLock & `::SendInput, {Blind}^`
CapsLock & ` Up::SendInput, {Blind}
Capslock & ,::SendInput, {Blind}^+{Tab}
Capslock & , up::SendInput, {Blind}
Capslock & .::SendInput, {Blind}^{Tab}
Capslock & . up::SendInput, {Blind}
CapsLock & `;::SendInput, {Blind}^;
CapsLock & `; Up::SendInput, {Blind}
CapsLock & /::SendInput, {Blind}^/
CapsLock & / Up::SendInput, {Blind}
CapsLock & |::SendInput, {Blind}^|
CapsLock & | Up::SendInput, {Blind}
CapsLock & '::SendInput, {Blind}^'
CapsLock & ' Up::SendInput, {Blind}
CapsLock & \::SendInput, {Blind}^\
CapsLock & \ Up::SendInput, {Blind}
CapsLock & [::SendInput, {Blind}^[
CapsLock & [ Up::SendInput, {Blind}
CapsLock & ]::SendInput, {Blind}^]
CapsLock & ] Up::SendInput, {Blind}
</pre>
