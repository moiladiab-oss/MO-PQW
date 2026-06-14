-- [[ MOILA933 - PLAYER INFO + BANG + SPECTATE ]] --

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenSvc = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- ============================================================
-- Remotes (lazy-find on first use)
-- ============================================================
local function fireCmd(cmd)
task.spawn(function()
pcall(function()
local hd = ReplicatedStorage:FindFirstChild("HDAdminHDClient")
if hd then
local sig = hd:FindFirstChild("Signals")
if sig then
local rem = sig:FindFirstChild("RequestCommandModification")
if rem then
if rem:IsA("RemoteFunction") then rem:InvokeServer(cmd)
else rem:FireServer(cmd) end
end
end
end
end)
pcall(function()
local re = ReplicatedStorage:FindFirstChild("RemoteEvents")
if re then
local ds = re:FindFirstChild("DataService")
if ds then ds:FireServer(cmd) end
end
end)
end)
end

-- ============================================================
-- Colors & helpers
-- ============================================================
local WHITE = Color3.fromRGB(255,255,255)
local DARK = Color3.fromRGB(3,5,22)
local BLUE_A = Color3.fromRGB(30,100,255)
local GREEN = Color3.fromRGB(50,220,80)
local YELLOW = Color3.fromRGB(255,210,30)
local RED = Color3.fromRGB(220,50,50)

local function corner(p,r) Instance.new("UICorner",p).CornerRadius=UDim.new(0,r or 10) end
local function stroke(p,col,t,tr)
local s=Instance.new("UIStroke",p); s.Color=col; s.Thickness=t or 1
s.Transparency=tr or 0; s.ApplyStrokeMode=Enum.ApplyStrokeMode.Border; return s
end
local function grad(p,c1,c2,rot)
local g=Instance.new("UIGradient",p); g.Color=ColorSequence.new(c1,c2); g.Rotation=rot or 90
end

local function styleBtn(btn,bgColor,gradColor)
btn.BackgroundColor3=bgColor; btn.Font=Enum.Font.GothamBold; btn.TextSize=13
btn.AutoButtonColor=false; btn.BorderSizePixel=0; btn.TextColor3=WHITE
corner(btn,10); grad(btn,bgColor,gradColor or bgColor,90); stroke(btn,WHITE,1,0.5)
btn.MouseEnter:Connect(function() TweenSvc:Create(btn,TweenInfo.new(0.12),{BackgroundTransparency=0.25}):Play() end)
btn.MouseLeave:Connect(function() TweenSvc:Create(btn,TweenInfo.new(0.12),{BackgroundTransparency=0}):Play() end)
btn.MouseButton1Down:Connect(function() TweenSvc:Create(btn,TweenInfo.new(0.07),{BackgroundTransparency=0.5}):Play() end)
btn.MouseButton1Up:Connect(function() TweenSvc:Create(btn,TweenInfo.new(0.12),{BackgroundTransparency=0}):Play() end)
end

local function makeDraggable(frame,handle)
local dragging,dragInput,dragStart,startPos
handle=handle or frame
handle.InputBegan:Connect(function(inp)
if inp.UserInputType==Enum.UserInputType.MouseButton1 or inp.UserInputType==Enum.UserInputType.Touch then
dragging=true; dragStart=inp.Position; startPos=frame.Position
inp.Changed:Connect(function() if inp.UserInputState==Enum.UserInputState.End then dragging=false end end)
end
end)
handle.InputChanged:Connect(function(inp)
if inp.UserInputType==Enum.UserInputType.MouseMovement or inp.UserInputType==Enum.UserInputType.Touch then dragInput=inp end
end)
UIS.InputChanged:Connect(function(inp)
if inp==dragInput and dragging then
local d=inp.Position-dragStart
frame.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+d.X,startPos.Y.Scale,startPos.Y.Offset+d.Y)
end
end)
end

-- ============================================================
-- ScreenGui
-- ============================================================
pcall(function() LocalPlayer.PlayerGui:FindFirstChild("MOILA_PlayerInfo"):Destroy() end)

local SG=Instance.new("ScreenGui"); SG.Name="MOILA_PlayerInfo"; SG.ResetOnSpawn=false
SG.IgnoreGuiInset=true; SG.ZIndexBehavior=Enum.ZIndexBehavior.Sibling
SG.DisplayOrder=9999; SG.Parent=LocalPlayer:WaitForChild("PlayerGui")

-- ============================================================
-- Main Frame 590 × 460
-- ============================================================
local MF=Instance.new("Frame")
MF.Size=UDim2.new(0,590,0,460); MF.Position=UDim2.new(0.5,-295,0.5,-230)
MF.BackgroundColor3=DARK; MF.BackgroundTransparency=0.05
MF.BorderSizePixel=0; MF.Active=true; MF.Parent=SG
corner(MF,18); grad(MF,Color3.fromRGB(4,9,34),Color3.fromRGB(2,4,16),145)

local mStk=stroke(MF,WHITE,2,0.4)
task.spawn(function()
while MF and MF.Parent do
local p=(math.sin(tick()2)+1)/2
mStk.Transparency=0.08+p0.72; mStk.Color=Color3.fromHSV((tick()*0.12)%1,0.7,1)
task.wait(0.03)
end
end)
makeDraggable(MF)

-- Title Bar
local TB=Instance.new("Frame"); TB.Size=UDim2.new(1,0,0,48)
TB.BackgroundColor3=Color3.fromRGB(6,18,70); TB.BackgroundTransparency=0.18
TB.BorderSizePixel=0; TB.Parent=MF; corner(TB,18)
grad(TB,Color3.fromRGB(10,32,105),Color3.fromRGB(4,13,50),90)
local TBFix=Instance.new("Frame"); TBFix.Size=UDim2.new(1,0,0.5,0); TBFix.Position=UDim2.new(0,0,0.5,0)
TBFix.BackgroundColor3=Color3.fromRGB(6,18,70); TBFix.BackgroundTransparency=0.18; TBFix.BorderSizePixel=0; TBFix.Parent=TB

local TLbl=Instance.new("TextLabel"); TLbl.Size=UDim2.new(1,-55,1,0); TLbl.Position=UDim2.new(0,14,0,0)
TLbl.BackgroundTransparency=1; TLbl.Text="🔍 MOILA933 — مراقبة لاعبين"
TLbl.Font=Enum.Font.GothamBold; TLbl.TextSize=14; TLbl.TextXAlignment=Enum.TextXAlignment.Left; TLbl.Parent=TB
task.spawn(function()
while TLbl and TLbl.Parent do TLbl.TextColor3=Color3.fromHSV((tick()*0.3)%1,0.6,1); task.wait(0.03) end
end)
makeDraggable(MF,TB)

local CloseBtn=Instance.new("TextButton"); CloseBtn.Size=UDim2.new(0,30,0,30); CloseBtn.Position=UDim2.new(1,-37,0,9)
CloseBtn.BackgroundColor3=Color3.fromRGB(170,30,30); CloseBtn.Text="✕"; CloseBtn.TextColor3=WHITE
CloseBtn.Font=Enum.Font.GothamBold; CloseBtn.TextSize=14; CloseBtn.AutoButtonColor=false
CloseBtn.BorderSizePixel=0; CloseBtn.ZIndex=10; CloseBtn.Parent=MF; corner(CloseBtn,8)
CloseBtn.MouseEnter:Connect(function() TweenSvc:Create(CloseBtn,TweenInfo.new(0.15),{BackgroundColor3=Color3.fromRGB(220,50,50)}):Play() end)
CloseBtn.MouseLeave:Connect(function() TweenSvc:Create(CloseBtn,TweenInfo.new(0.15),{BackgroundColor3=Color3.fromRGB(170,30,30)}):Play() end)
CloseBtn.MouseButton1Click:Connect(function()
MF.Visible = false
end)

-- ============================================================
-- LEFT — Player List (170 wide)
-- ============================================================
local LP=Instance.new("Frame"); LP.Size=UDim2.new(0,172,1,-60); LP.Position=UDim2.new(0,8,0,54)
LP.BackgroundColor3=Color3.fromRGB(4,8,30); LP.BackgroundTransparency=0.25
LP.BorderSizePixel=0; LP.Parent=MF; corner(LP,12); stroke(LP,BLUE_A,1,0.45)

local LPTitle=Instance.new("TextLabel"); LPTitle.Size=UDim2.new(1,0,0,30)
LPTitle.BackgroundColor3=Color3.fromRGB(8,20,75); LPTitle.BackgroundTransparency=0.2
LPTitle.BorderSizePixel=0; LPTitle.Text="👥 اختر لاعب"
LPTitle.Font=Enum.Font.GothamBold; LPTitle.TextSize=12; LPTitle.TextColor3=WHITE; LPTitle.Parent=LP
corner(LPTitle,12); grad(LPTitle,Color3.fromRGB(12,35,110),Color3.fromRGB(5,15,55),90)
local LPTFix=Instance.new("Frame"); LPTFix.Size=UDim2.new(1,0,0.5,0); LPTFix.Position=UDim2.new(0,0,0.5,0)
LPTFix.BackgroundColor3=Color3.fromRGB(8,20,75); LPTFix.BackgroundTransparency=0.2; LPTFix.BorderSizePixel=0; LPTFix.Parent=LPTitle

local PSF=Instance.new("ScrollingFrame"); PSF.Size=UDim2.new(1,-6,1,-36); PSF.Position=UDim2.new(0,3,0,32)
PSF.BackgroundTransparency=1; PSF.BorderSizePixel=0; PSF.ScrollBarThickness=4
PSF.ScrollBarImageColor3=BLUE_A; PSF.CanvasSize=UDim2.new(); PSF.AutomaticCanvasSize=Enum.AutomaticSize.Y; PSF.Parent=LP

Instance.new("UIListLayout",PSF).Padding=UDim.new(0,5)
local PLPad=Instance.new("UIPadding",PSF); PLPad.PaddingTop=UDim.new(0,4)
PLPad.PaddingLeft=UDim.new(0,2); PLPad.PaddingRight=UDim.new(0,2)

-- ============================================================
-- RIGHT — Info card + 3 action buttons
-- ============================================================
local RP=Instance.new("Frame"); RP.Size=UDim2.new(1,-188,1,-66); RP.Position=UDim2.new(0,184,0,54)
RP.BackgroundColor3=Color3.fromRGB(4,8,30); RP.BackgroundTransparency=0.2
RP.BorderSizePixel=0; RP.Parent=MF; corner(RP,12)
grad(RP,Color3.fromRGB(6,14,50),Color3.fromRGB(3,6,25),135)
local iStk=stroke(RP,BLUE_A,1.5,0.4)
task.spawn(function()
while RP and RP.Parent do
local p=(math.sin(tick()2.2)+1)/2; iStk.Transparency=0.1+p0.7; task.wait(0.04)
end
end)

-- Empty hint
local EmptyLbl=Instance.new("TextLabel"); EmptyLbl.Size=UDim2.new(1,0,1,0)
EmptyLbl.BackgroundTransparency=1; EmptyLbl.Text="← اضغط على اسم لاعب"
EmptyLbl.Font=Enum.Font.GothamBold; EmptyLbl.TextSize=14
EmptyLbl.TextColor3=Color3.fromRGB(80,110,170); EmptyLbl.Parent=RP

-- Avatar circle
local AvFrame=Instance.new("Frame"); AvFrame.Size=UDim2.new(0,100,0,100)
AvFrame.Position=UDim2.new(0.5,-50,0,14); AvFrame.BackgroundColor3=Color3.fromRGB(5,10,35)
AvFrame.BorderSizePixel=0; AvFrame.Visible=false; AvFrame.Parent=RP; corner(AvFrame,999)
local avStk=stroke(AvFrame,BLUE_A,2.5,0.2)
task.spawn(function()
while AvFrame and AvFrame.Parent do
avStk.Color=Color3.fromHSV((tick()*0.2)%1,0.8,1)
local p=(math.sin(tick()3)+1)/2; avStk.Transparency=p0.5; task.wait(0.04)
end
end)
local AvImg=Instance.new("ImageLabel"); AvImg.Size=UDim2.new(1,-6,1,-6)
AvImg.Position=UDim2.new(0,3,0,3); AvImg.BackgroundTransparency=1; AvImg.Parent=AvFrame; corner(AvImg,999)

-- Info labels
local function infoLbl(parent,y,sz,col)
local l=Instance.new("TextLabel"); l.Size=UDim2.new(1,-16,0,20); l.Position=UDim2.new(0,8,0,y)
l.BackgroundTransparency=1; l.Font=Enum.Font.GothamBold; l.TextSize=sz or 12
l.TextColor3=col or WHITE; l.TextXAlignment=Enum.TextXAlignment.Center
l.TextTruncate=Enum.TextTruncate.AtEnd; l.Visible=false; l.Parent=parent; return l
end

local LName = infoLbl(RP, 118, 16, WHITE)
local LDisplay = infoLbl(RP, 138, 11, Color3.fromRGB(140,190,255))
local LUID = infoLbl(RP, 156, 10, Color3.fromRGB(100,150,210))
local LAge = infoLbl(RP, 172, 10, Color3.fromRGB(100,150,210))
local LTeam = infoLbl(RP, 188, 10, Color3.fromRGB(100,150,210))

-- HP bar
local HpBg=Instance.new("Frame"); HpBg.Size=UDim2.new(1,-20,0,16); HpBg.Position=UDim2.new(0,10,0,212)
HpBg.BackgroundColor3=Color3.fromRGB(10,10,25); HpBg.BackgroundTransparency=0.3
HpBg.BorderSizePixel=0; HpBg.Visible=false; HpBg.Parent=RP; corner(HpBg,6)
local HpFill=Instance.new("Frame"); HpFill.Size=UDim2.new(1,0,1,0); HpFill.BackgroundColor3=GREEN
HpFill.BorderSizePixel=0; HpFill.Parent=HpBg; corner(HpFill,6)
local HpTxt=Instance.new("TextLabel"); HpTxt.Size=UDim2.new(1,0,1,0); HpTxt.BackgroundTransparency=1
HpTxt.Text="❤️ 100 / 100"; HpTxt.Font=Enum.Font.GothamBold; HpTxt.TextSize=9; HpTxt.TextColor3=WHITE; HpTxt.Parent=HpBg

-- Position chip
local PosLbl=infoLbl(RP, 234, 9, Color3.fromRGB(90,140,200))

-- ============================================================
-- 3 Action Buttons
-- ============================================================
local BtnRow=Instance.new("Frame"); BtnRow.Size=UDim2.new(1,-20,0,44); BtnRow.Position=UDim2.new(0,10,1,-50)
BtnRow.BackgroundTransparency=1; BtnRow.Parent=RP

local BtnGrid=Instance.new("UIGridLayout"); BtnGrid.CellSize=UDim2.new(0.33,-5,1,-4)
BtnGrid.CellPadding=UDim2.new(0,6,0,0); BtnGrid.HorizontalAlignment=Enum.HorizontalAlignment.Center
BtnGrid.VerticalAlignment=Enum.VerticalAlignment.Center; BtnGrid.Parent=BtnRow

-- Bang button
local BangBtn=Instance.new("TextButton"); BangBtn.Text="💥 بانق"; BangBtn.Parent=BtnRow
styleBtn(BangBtn,Color3.fromRGB(180,20,20),Color3.fromRGB(100,10,10))

-- Spectate button
local SpecBtn=Instance.new("TextButton"); SpecBtn.Text="👁 مراقبة"; SpecBtn.Parent=BtnRow
styleBtn(SpecBtn,Color3.fromRGB(30,100,180),Color3.fromRGB(15,50,110))

-- Stop button
local StopBtn=Instance.new("TextButton"); StopBtn.Text="⏹ إيقاف"; StopBtn.Parent=BtnRow
styleBtn(StopBtn,Color3.fromRGB(50,50,80),Color3.fromRGB(25,25,50))

-- ============================================================
-- State
-- ============================================================
local selectedPlayer = nil
local bangRunning = false
local spectating = false
local liveConn = nil
local selectedBtn = nil

local function noTarget()
return not selectedPlayer or not selectedPlayer.Parent
end

-- ============================================================
-- Live HP / Position updater
-- ============================================================
local function startLiveUpdate()
if liveConn then liveConn:Disconnect(); liveConn=nil end
liveConn=RunService.Heartbeat:Connect(function()
if noTarget() then return end
local p=selectedPlayer
local char=p.Character
if char then
local hum=char:FindFirstChildOfClass("Humanoid")
if hum then
local hp=math.floor(hum.Health); local mx=math.max(math.floor(hum.MaxHealth),1)
local pct=hp/mx
TweenSvc:Create(HpFill,TweenInfo.new(0.3),{
Size=UDim2.new(pct,0,1,0),
BackgroundColor3=pct>0.6 and GREEN or pct>0.3 and YELLOW or RED
}):Play()
HpTxt.Text="❤️ "..hp.." / "..mx
end
local root=char:FindFirstChild("HumanoidRootPart")
if root then
local pos=root.Position
PosLbl.Text=string.format("📍 X:%.0f Y:%.0f Z:%.0f",pos.X,pos.Y,pos.Z)
PosLbl.Visible=true
end
end
end)
end

-- ============================================================
-- Show player info
-- ============================================================
local function showInfo(p)
selectedPlayer=p

EmptyLbl.Visible=false
AvFrame.Visible=true
LName.Visible=true; LDisplay.Visible=true; LUID.Visible=true
LAge.Visible=true; LTeam.Visible=true; HpBg.Visible=true

LName.Text="👤 "..p.Name
LDisplay.Text="📛 "..p.DisplayName
LUID.Text="🆔 "..tostring(p.UserId)

-- Account age async
task.spawn(function()
local ok,age=pcall(function() return p.AccountAge end)
if ok and LAge and LAge.Parent then
LAge.Text="📅 عمر الحساب: "..age.." يوم"
end
end)

-- Team
LTeam.Text = p.Team and ("🏳️ "..p.Team.Name) or "🏳️ بدون فريق"

-- Avatar async
AvImg.Image=""
task.spawn(function()
local ok,th=pcall(function()
return Players:GetUserThumbnailAsync(p.UserId,Enum.ThumbnailType.AvatarBust,Enum.ThumbnailSize.Size420x420)
end)
if ok and th and AvImg and AvImg.Parent then AvImg.Image=th end
end)

startLiveUpdate()

-- flash border
TweenSvc:Create(iStk,TweenInfo.new(0.1),{Color=WHITE,Transparency=0}):Play()
task.delay(0.25,function()
TweenSvc:Create(iStk,TweenInfo.new(0.3),{Color=BLUE_A,Transparency=0.4}):Play()
end)
end

-- ============================================================
-- BANG button logic
-- ============================================================
BangBtn.MouseButton1Click:Connect(function()
if noTarget() then return end
if bangRunning then
bangRunning=false
BangBtn.Text="💥 بانق"
grad(BangBtn,Color3.fromRGB(180,20,20),Color3.fromRGB(100,10,10),90)
return
end
bangRunning=true
BangBtn.Text="🛑 إيقاف بانق"
grad(BangBtn,Color3.fromRGB(30,100,30),Color3.fromRGB(15,55,15),90)

task.spawn(function()
while bangRunning and not noTarget() do
local name=selectedPlayer.Name
fireCmd(";lag "..name)
fireCmd(";crash "..name)
fireCmd(";freeze "..name)
fireCmd(";loopkill "..name)
fireCmd(";explode "..name)
task.wait()
end
bangRunning=false
if BangBtn and BangBtn.Parent then
BangBtn.Text="💥 بانق"
grad(BangBtn,Color3.fromRGB(180,20,20),Color3.fromRGB(100,10,10),90)
end
end)
end)

-- ============================================================
-- SPECTATE button logic
-- ============================================================
SpecBtn.MouseButton1Click:Connect(function()
if noTarget() then return end

if spectating then
-- Return to own camera
spectating=false
SpecBtn.Text="👁 مراقبة"
grad(SpecBtn,Color3.fromRGB(30,100,180),Color3.fromRGB(15,50,110),90)
Camera.CameraType=Enum.CameraType.Custom
local myChar=LocalPlayer.Character
if myChar then
local myHum=myChar:FindFirstChildOfClass("Humanoid")
if myHum then Camera.CameraSubject=myHum end
end
return
end

local char=selectedPlayer.Character
if not char then return end
local hum=char:FindFirstChildOfClass("Humanoid")
if not hum then return end

spectating=true
SpecBtn.Text="🔴 إيقاف مراقبة"
grad(SpecBtn,Color3.fromRGB(180,80,10),Color3.fromRGB(100,40,5),90)

Camera.CameraType=Enum.CameraType.Custom
Camera.CameraSubject=hum

-- auto-follow if player respawns
selectedPlayer.CharacterAdded:Connect(function(newChar)
if spectating then
task.wait(0.1)
local newHum=newChar:WaitForChild("Humanoid",5)
if newHum and spectating then Camera.CameraSubject=newHum end
end
end)
end)

-- ============================================================
-- STOP button logic
-- ============================================================
StopBtn.MouseButton1Click:Connect(function()
-- Stop bang
bangRunning=false
BangBtn.Text="💥 بانق"
grad(BangBtn,Color3.fromRGB(180,20,20),Color3.fromRGB(100,10,10),90)

-- Stop spectate
if spectating then
spectating=false
SpecBtn.Text="👁 مراقبة"
grad(SpecBtn,Color3.fromRGB(30,100,180),Color3.fromRGB(15,50,110),90)
Camera.CameraType=Enum.CameraType.Custom
local myChar=LocalPlayer.Character
if myChar then
local myHum=myChar:FindFirstChildOfClass("Humanoid")
if myHum then Camera.CameraSubject=myHum end
end
end
end)

-- ============================================================
-- Player list builder
-- ============================================================
local function buildList()
for _,v in pairs(PSF:GetChildren()) do
if v:IsA("TextButton") or v:IsA("Frame") then v:Destroy() end
end

local all=Players:GetPlayers()
for i,p in ipairs(all) do
local isLocal=(p==LocalPlayer)

local row=Instance.new("TextButton"); row.Size=UDim2.new(1,-4,0,58)
row.BackgroundColor3=Color3.fromRGB(5,10,38); row.BackgroundTransparency=0.25
row.BorderSizePixel=0; row.Text=""; row.AutoButtonColor=false; row.LayoutOrder=i; row.Parent=PSF
corner(row,10); grad(row,Color3.fromRGB(8,18,60),Color3.fromRGB(3,6,25),135)
local rStk=stroke(row,isLocal and GREEN or BLUE_A,1.5,isLocal and 0.2 or 0.6)

-- Avatar
local mAv=Instance.new("ImageLabel"); mAv.Size=UDim2.new(0,40,0,40)
mAv.Position=UDim2.new(0,6,0.5,-20); mAv.BackgroundColor3=Color3.fromRGB(5,10,35)
mAv.BorderSizePixel=0; mAv.Parent=row; corner(mAv,999)
stroke(mAv,isLocal and GREEN or BLUE_A,1.5,0.35)
task.spawn(function()
local ok,th=pcall(function()
return Players:GetUserThumbnailAsync(p.UserId,Enum.ThumbnailType.HeadShot,Enum.ThumbnailSize.Size100x100)
end)
if ok and th and mAv and mAv.Parent then mAv.Image=th end
end)

-- Labels
local nL=Instance.new("TextLabel"); nL.Size=UDim2.new(1,-54,0,22)
nL.Position=UDim2.new(0,52,0,8); nL.BackgroundTransparency=1
nL.Text=(isLocal and "👑 " or "")..p.Name; nL.Font=Enum.Font.GothamBold; nL.TextSize=11
nL.TextColor3=isLocal and GREEN or WHITE; nL.TextXAlignment=Enum.TextXAlignment.Left
nL.TextTruncate=Enum.TextTruncate.AtEnd; nL.Parent=row

local dL=Instance.new("TextLabel"); dL.Size=UDim2.new(1,-54,0,16)
dL.Position=UDim2.new(0,52,0,30); dL.BackgroundTransparency=1
dL.Text=p.DisplayName; dL.Font=Enum.Font.Gotham; dL.TextSize=9
dL.TextColor3=Color3.fromRGB(100,140,200); dL.TextXAlignment=Enum.TextXAlignment.Left
dL.TextTruncate=Enum.TextTruncate.AtEnd; dL.Parent=row

-- Click
row.MouseButton1Click:Connect(function()
if selectedBtn and selectedBtn~=row and selectedBtn.Parent then
selectedBtn.BackgroundColor3=Color3.fromRGB(5,10,38)
local s=selectedBtn:FindFirstChildOfClass("UIStroke")
if s then s.Color=BLUE_A; s.Thickness=1.5; s.Transparency=0.6 end
end
selectedBtn=row
TweenSvc:Create(row,TweenInfo.new(0.15),{BackgroundColor3=Color3.fromRGB(12,38,140)}):Play()
rStk.Color=WHITE; rStk.Thickness=2; rStk.Transparency=0

-- If spectating someone else, stop spectate first
if spectating and selectedPlayer~=p then
spectating=false
SpecBtn.Text="👁 مراقبة"
grad(SpecBtn,Color3.fromRGB(30,100,180),Color3.fromRGB(15,50,110),90)
Camera.CameraType=Enum.CameraType.Custom
local mc=LocalPlayer.Character
if mc then local mh=mc:FindFirstChildOfClass("Humanoid"); if mh then Camera.CameraSubject=mh end end
end

showInfo(p)
end)

row.MouseEnter:Connect(function()
if row~=selectedBtn then TweenSvc:Create(row,TweenInfo.new(0.12),{BackgroundTransparency=0.1}):Play() end
end)
row.MouseLeave:Connect(function()
if row~=selectedBtn then TweenSvc:Create(row,TweenInfo.new(0.12),{BackgroundTransparency=0.25}):Play() end
end)
end
end

buildList()
Players.PlayerAdded:Connect(function() task.wait(0.5); buildList() end)
Players.PlayerRemoving:Connect(function()
task.wait(0.1); buildList()
end)

-- ============================================================
-- Bottom branding
-- ============================================================
local BN=Instance.new("TextLabel"); BN.Size=UDim2.new(1,-20,0,14)
BN.Position=UDim2.new(0,10,1,-16); BN.BackgroundTransparency=1
BN.Text="made by MOILA933 ✨ | Player Info + Bang + Spectate"
BN.Font=Enum.Font.GothamBold; BN.TextSize=9; BN.Parent=MF
task.spawn(function()
while BN and BN.Parent do
BN.TextColor3=Color3.fromHSV((tick()*0.22)%1,0.5,0.8); task.wait(0.04)
end
end)

-- ============================================================
-- Toggle Button (دائرة التشغيل والإلغاء)
-- ============================================================
local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Name = "ToggleBtn"
ToggleBtn.Size = UDim2.new(0, 50, 0, 50)
ToggleBtn.Position = UDim2.new(0, 20, 0.5, -25)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(15, 50, 160)
ToggleBtn.Text = "🔍"
ToggleBtn.TextColor3 = WHITE
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.TextSize = 20
ToggleBtn.AutoButtonColor = true
ToggleBtn.Parent = SG

corner(ToggleBtn, 50)
stroke(ToggleBtn, WHITE, 2, 0)
makeDraggable(ToggleBtn)

ToggleBtn.MouseButton1Click:Connect(function()
MF.Visible = not MF.Visible
end)

