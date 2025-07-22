-- Services
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

-- Wait for LocalPlayer
local player = Players.LocalPlayer
while not player do
    Players.PlayerAdded:Wait()
    player = Players.LocalPlayer
end

-- Constants
local API_URL = "https://scriptblox.com/api/script/fetch"
local CREATOR_NAME = "dark sistem"
local CREATOR_USERNAME = "bijisalakbulat"
local CREATOR_USERID = 154739733
local CREATOR_PROFILE_LINK = "https://www.roblox.com/users/" .. CREATOR_USERID .. "/profile"
local BUG_REPORT_LINK = "https://chat.whatsapp.com/FV0MoulxZ6SEeppU8qKmMh?mode=ac_t" -- Ganti dengan grup WhatsApp resmi

-- Variables
local currentPage = 1
local maxPerPage = 20
local currentSearch = ""
local totalPages = 1

-- Helper Functions

local function createNotification(message)
    local playerGui = player:WaitForChild("PlayerGui")
    local screenGui = playerGui:FindFirstChild("ScriptNotifGui")
    if not screenGui then
        screenGui = Instance.new("ScreenGui")
        screenGui.Name = "ScriptNotifGui"
        screenGui.Parent = playerGui
        screenGui.ResetOnSpawn = false
    end

    local notifFrame = Instance.new("Frame")
    notifFrame.BackgroundColor3 = Color3.fromRGB(30,30,30)
    notifFrame.BackgroundTransparency = 0.3
    notifFrame.Size = UDim2.new(0, 300, 0, 50)
    notifFrame.Position = UDim2.new(0.5, -150, 0.8, 0)
    notifFrame.AnchorPoint = Vector2.new(0.5,0)
    notifFrame.ZIndex = 10
    notifFrame.Parent = screenGui

    local notifText = Instance.new("TextLabel")
    notifText.Size = UDim2.new(1, -20, 1, 0)
    notifText.Position = UDim2.new(0, 10, 0, 0)
    notifText.BackgroundTransparency = 1
    notifText.TextColor3 = Color3.fromRGB(255,255,255)
    notifText.Font = Enum.Font.GothamBold
    notifText.TextSize = 18
    notifText.Text = message
    notifText.Parent = notifFrame

    delay(3, function()
        notifFrame:Destroy()
    end)
end

local function setClipboard(text)
    pcall(function()
        setclipboard(text)
    end)
end

-- Drag and Drop Functionality for GUI Frames
local function makeDraggable(frame)
    local dragging = false
    local dragInput, dragStart, startPos

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(
                math.clamp(startPos.X.Scale, 0, 1),
                math.clamp(startPos.X.Offset + delta.X, 0, workspace.CurrentCamera.ViewportSize.X - frame.AbsoluteSize.X),
                math.clamp(startPos.Y.Scale, 0, 1),
                math.clamp(startPos.Y.Offset + delta.Y, 0, workspace.CurrentCamera.ViewportSize.Y - frame.AbsoluteSize.Y)
            )
        end
    end)
end

-- Build Main GUI

local playerGui = player:WaitForChild("PlayerGui")
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ScriptSearchGUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

-- Background frame
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 600, 0, 500)
mainFrame.Position = UDim2.new(0.5, -300, 0.3, -250)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui
makeDraggable(mainFrame)

-- BETA LABEL
local betaLabel = Instance.new("TextLabel")
betaLabel.Text = "BETA VERSION"
betaLabel.Font = Enum.Font.GothamBold
betaLabel.TextSize = 16
betaLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
betaLabel.BackgroundTransparency = 1
betaLabel.Size = UDim2.new(1, 0, 0, 25)
betaLabel.Position = UDim2.new(0, 0, 0, 0)
betaLabel.Parent = mainFrame

-- Title Label
local titleLabel = Instance.new("TextLabel")
titleLabel.Text = "Script Search GUI"
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextSize = 22
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.BackgroundTransparency = 1
titleLabel.Size = UDim2.new(1, 0, 0, 30)
titleLabel.Position = UDim2.new(0, 0, 0, 30)
titleLabel.Parent = mainFrame

-- Search Box
local searchBox = Instance.new("TextBox")
searchBox.PlaceholderText = "Search scripts..."
searchBox.Font = Enum.Font.Gotham
searchBox.TextSize = 18
searchBox.TextColor3 = Color3.fromRGB(255, 255, 255)
searchBox.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
searchBox.BorderSizePixel = 0
searchBox.ClearTextOnFocus = false
searchBox.Size = UDim2.new(0.7, 0, 0, 35)
searchBox.Position = UDim2.new(0, 15, 0, 70)
searchBox.Parent = mainFrame

-- Search Button
local searchBtn = Instance.new("TextButton")
searchBtn.Text = "Search"
searchBtn.Font = Enum.Font.GothamBold
searchBtn.TextSize = 18
searchBtn.TextColor3 = Color3.new(1, 1, 1)
searchBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
searchBtn.BorderSizePixel = 0
searchBtn.Size = UDim2.new(0.28, 0, 0, 35)
searchBtn.Position = UDim2.new(0.72, 0, 0, 70)
searchBtn.Parent = mainFrame

-- Results Frame (Scrollable)
local resultsFrame = Instance.new("ScrollingFrame")
resultsFrame.Name = "ResultsFrame"
resultsFrame.Size = UDim2.new(1, -30, 0, 320)
resultsFrame.Position = UDim2.new(0, 15, 0, 115)
resultsFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
resultsFrame.BorderSizePixel = 0
resultsFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
resultsFrame.ScrollBarThickness = 6
resultsFrame.ScrollingDirection = Enum.ScrollingDirection.Y
resultsFrame.Parent = mainFrame

local resultsLayout = Instance.new("UIListLayout")
resultsLayout.SortOrder = Enum.SortOrder.LayoutOrder
resultsLayout.Padding = UDim.new(0, 8)
resultsLayout.Parent = resultsFrame

resultsLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    resultsFrame.CanvasSize = UDim2.new(0, 0, 0, resultsLayout.AbsoluteContentSize.Y + 10)
end)

-- Pagination Buttons
local paginationFrame = Instance.new("Frame")
paginationFrame.Size = UDim2.new(1, -30, 0, 40)
paginationFrame.Position = UDim2.new(0, 15, 1, -60)
paginationFrame.BackgroundTransparency = 1
paginationFrame.Parent = mainFrame

local prevBtn = Instance.new("TextButton")
prevBtn.Text = "< Prev"
prevBtn.Font = Enum.Font.GothamBold
prevBtn.TextSize = 16
prevBtn.TextColor3 = Color3.new(1, 1, 1)
prevBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 90)
prevBtn.BorderSizePixel = 0
prevBtn.Size = UDim2.new(0.2, 0, 1, 0)
prevBtn.Position = UDim2.new(0, 0, 0, 0)
prevBtn.Parent = paginationFrame

local pageLabel = Instance.new("TextLabel")
pageLabel.Text = "Page 1 / 1"
pageLabel.Font = Enum.Font.GothamBold
pageLabel.TextSize = 18
pageLabel.TextColor3 = Color3.new(1, 1, 1)
pageLabel.BackgroundTransparency = 1
pageLabel.Size = UDim2.new(0.6, 0, 1, 0)
pageLabel.Position = UDim2.new(0.25, 0, 0, 0)
pageLabel.Parent = paginationFrame

local nextBtn = Instance.new("TextButton")
nextBtn.Text = "Next >"
nextBtn.Font = Enum.Font.GothamBold
nextBtn.TextSize = 16
nextBtn.TextColor3 = Color3.new(1, 1, 1)
nextBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 90)
nextBtn.BorderSizePixel = 0
nextBtn.Size = UDim2.new(0.2, 0, 1, 0)
nextBtn.Position = UDim2.new(0.85, 0, 0, 0)
nextBtn.Parent = paginationFrame

-- Detail Frame (Hidden by default)
local detailFrame = Instance.new("Frame")
detailFrame.Name = "DetailFrame"
detailFrame.Size = UDim2.new(0, 580, 0, 400)
detailFrame.Position = UDim2.new(0.5, -290, 0.3, 50)
detailFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
detailFrame.BorderSizePixel = 0
detailFrame.Visible = false
detailFrame.Parent = screenGui
makeDraggable(detailFrame)

local detailTitle = Instance.new("TextLabel")
detailTitle.Text = ""
detailTitle.Font = Enum.Font.GothamBold
detailTitle.TextSize = 22
detailTitle.TextColor3 = Color3.new(1, 1, 1)
detailTitle.BackgroundTransparency = 1
detailTitle.Size = UDim2.new(1, -40, 0, 35)
detailTitle.Position = UDim2.new(0, 20, 0, 15)
detailTitle.TextXAlignment = Enum.TextXAlignment.Left
detailTitle.Parent = detailFrame

local detailDescription = Instance.new("TextLabel")
detailDescription.Text = ""
detailDescription.Font = Enum.Font.Gotham
detailDescription.TextSize = 16
detailDescription.TextColor3 = Color3.new(1, 1, 1)
detailDescription.BackgroundTransparency = 1
detailDescription.Size = UDim2.new(1, -40, 0, 120)
detailDescription.Position = UDim2.new(0, 20, 0, 60)
detailDescription.TextWrapped = true
detailDescription.TextYAlignment = Enum.TextYAlignment.Top
detailDescription.TextXAlignment = Enum.TextXAlignment.Left
detailDescription.Parent = detailFrame

local detailScriptBox = Instance.new("TextBox")
detailScriptBox.Text = ""
detailScriptBox.Font = Enum.Font.Code
detailScriptBox.TextSize = 14
detailScriptBox.TextColor3 = Color3.fromRGB(255, 255, 255)
detailScriptBox.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
detailScriptBox.ClearTextOnFocus = false
detailScriptBox.MultiLine = true
detailScriptBox.Size = UDim2.new(1, -40, 0, 190)
detailScriptBox.Position = UDim2.new(0, 20, 0, 190)
detailScriptBox.TextWrapped = true
detailScriptBox.Parent = detailFrame

local closeDetailBtn = Instance.new("TextButton")
closeDetailBtn.Text = "Close"
closeDetailBtn.Font = Enum.Font.GothamBold
closeDetailBtn.TextSize = 18
closeDetailBtn.TextColor3 = Color3.new(1, 1, 1)
closeDetailBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
closeDetailBtn.BorderSizePixel = 0
closeDetailBtn.Size = UDim2.new(0, 80, 0, 35)
closeDetailBtn.Position = UDim2.new(1, -90, 0, 15)
closeDetailBtn.Parent = detailFrame

local copyScriptBtn = Instance.new("TextButton")
copyScriptBtn.Text = "Copy Script"
copyScriptBtn.Font = Enum.Font.GothamBold
copyScriptBtn.TextSize = 16
copyScriptBtn.TextColor3 = Color3.new(1, 1, 1)
copyScriptBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 0)
copyScriptBtn.BorderSizePixel = 0
copyScriptBtn.Size = UDim2.new(0, 110, 0, 35)
copyScriptBtn.Position = UDim2.new(1, -210, 0, 15)
copyScriptBtn.Parent = detailFrame

local openCreatorProfileBtn = Instance.new("TextButton")
openCreatorProfileBtn.Text = "Creator Profile"
openCreatorProfileBtn.Font = Enum.Font.GothamBold
openCreatorProfileBtn.TextSize = 16
openCreatorProfileBtn.TextColor3 = Color3.new(1, 1, 1)
openCreatorProfileBtn.BackgroundColor3 = Color3.fromRGB(0, 70, 150)
openCreatorProfileBtn.BorderSizePixel = 0
openCreatorProfileBtn.Size = UDim2.new(0, 130, 0, 35)
openCreatorProfileBtn.Position = UDim2.new(0, 20, 1, -50)
openCreatorProfileBtn.Parent = detailFrame

local reportBugBtn = Instance.new("TextButton")
reportBugBtn.Text = "Report Bug"
reportBugBtn.Font = Enum.Font.GothamBold
reportBugBtn.TextSize = 16
reportBugBtn.TextColor3 = Color3.new(1, 1, 1)
reportBugBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 0)
reportBugBtn.BorderSizePixel = 0
reportBugBtn.Size = UDim2.new(0, 130, 0, 35)
reportBugBtn.Position = UDim2.new(1, -150, 1, -50)
reportBugBtn.Parent = detailFrame

-- Functions to render scripts

local function clearResults()
    for _, child in ipairs(resultsFrame:GetChildren()) do
        if child:IsA("Frame") then
            child:Destroy()
        end
    end
end

local function createScriptEntry(scriptData)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 90)
    frame.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    frame.BorderSizePixel = 0

    local titleLabel = Instance.new("TextLabel")
    titleLabel.Text = scriptData.name or "Unknown Script"
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.TextSize = 18
    titleLabel.TextColor3 = Color3.new(1, 1, 1)
    titleLabel.BackgroundTransparency = 1
    titleLabel.Size = UDim2.new(1, -100, 0, 25)
    titleLabel.Position = UDim2.new(0, 10, 0, 5)
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Parent = frame

    local creatorLabel = Instance.new("TextLabel")
    creatorLabel.Text = "By: " .. (scriptData.creator or "Unknown")
    creatorLabel.Font = Enum.Font.Gotham
    creatorLabel.TextSize = 14
    creatorLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    creatorLabel.BackgroundTransparency = 1
    creatorLabel.Size = UDim2.new(1, -100, 0, 20)
    creatorLabel.Position = UDim2.new(0, 10, 0, 35)
    creatorLabel.TextXAlignment = Enum.TextXAlignment.Left
    creatorLabel.Parent = frame

    local copyBtn = Instance.new("TextButton")
    copyBtn.Text = "Copy Script"
    copyBtn.Font = Enum.Font.GothamBold
    copyBtn.TextSize = 14
    copyBtn.TextColor3 = Color3.new(1, 1, 1)
    copyBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
    copyBtn.BorderSizePixel = 0
    copyBtn.Size = UDim2.new(0, 90, 0, 25)
    copyBtn.Position = UDim2.new(1, -100, 1, -30)
    copyBtn.Parent = frame

    local detailsBtn = Instance.new("TextButton")
    detailsBtn.Text = "Details"
    detailsBtn.Font = Enum.Font.GothamBold
    detailsBtn.TextSize = 14
    detailsBtn.TextColor3 = Color3.new(1, 1, 1)
    detailsBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    detailsBtn.BorderSizePixel = 0
    detailsBtn.Size = UDim2.new(0, 90, 0, 25)
    detailsBtn.Position = UDim2.new(1, -200, 1, -30)
    detailsBtn.Parent = frame

    -- Click events
    copyBtn.MouseButton1Click:Connect(function()
        setClipboard(scriptData.script or "")
        createNotification("Script copied to clipboard!")
    end)

    detailsBtn.MouseButton1Click:Connect(function()
        detailTitle.Text = scriptData.name or "No Name"
        detailDescription.Text = scriptData.description or "No Description"
        detailScriptBox.Text = scriptData.script or ""
        detailFrame.Visible = true
    end)

    return frame
end

-- Fetch scripts from API
local function fetchScripts(search, page)
    local url = API_URL .. "?search=" .. HttpService:UrlEncode(search) .. "&page=" .. tostring(page) .. "&limit=" .. tostring(maxPerPage)
    local success, response = pcall(function()
        return game:HttpGet(url)
    end)

    if success and response then
        local data = HttpService:JSONDecode(response)
        if data and data.scripts then
            return data.scripts, data.total or 0
        end
    end

    return {}, 0
end

-- Render scripts list
local function renderScripts()
    clearResults()

    local scripts, totalCount = fetchScripts(currentSearch, currentPage)
    if not scripts or #scripts == 0 then
        local emptyLabel = Instance.new("TextLabel")
        emptyLabel.Text = "No scripts found."
        emptyLabel.Font = Enum.Font.Gotham
        emptyLabel.TextSize = 18
        emptyLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        emptyLabel.BackgroundTransparency = 1
        emptyLabel.Size = UDim2.new(1, 0, 0, 30)
        emptyLabel.Position = UDim2.new(0, 0, 0, 10)
        emptyLabel.Parent = resultsFrame
        totalPages = 1
        pageLabel.Text = "Page 1 / 1"
        return
    end

    for _, scriptData in ipairs(scripts) do
        local entry = createScriptEntry(scriptData)
        entry.Parent = resultsFrame
    end

    totalPages = math.max(1, math.ceil(totalCount / maxPerPage))
    pageLabel.Text = "Page " .. currentPage .. " / " .. totalPages
end

-- Button Connections

searchBtn.MouseButton1Click:Connect(function()
    currentSearch = searchBox.Text or ""
    currentPage = 1
    renderScripts()
end)

prevBtn.MouseButton1Click:Connect(function()
    if currentPage > 1 then
        currentPage = currentPage - 1
        renderScripts()
    end
end)

nextBtn.MouseButton1Click:Connect(function()
    if currentPage < totalPages then
        currentPage = currentPage + 1
        renderScripts()
    end
end)

closeDetailBtn.MouseButton1Click:Connect(function()
    detailFrame.Visible = false
end)

copyScriptBtn.MouseButton1Click:Connect(function()
    local scriptText = detailScriptBox.Text
    setClipboard(scriptText)
    createNotification("Script copied to clipboard!")
end)

openCreatorProfileBtn.MouseButton1Click:Connect(function()
    -- Open creator profile in browser
    if syn and syn.request then
        syn.request({
            Url = CREATOR_PROFILE_LINK,
            Method = "GET"
        })
    else
        -- fallback
        createNotification("Open creator profile: " .. CREATOR_PROFILE_LINK)
    end
end)

reportBugBtn.MouseButton1Click:Connect(function()
    -- Open WhatsApp group (if executor supports)
    if syn and syn.request then
        syn.request({
            Url = BUG_REPORT_LINK,
            Method = "GET"
        })
    else
        createNotification("Join bug report group: " .. BUG_REPORT_LINK)
    end
end)

-- Init render first page
renderScripts()
