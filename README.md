-- Create the ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FOVChangerGUI"
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
screenGui.ResetOnSpawn = false  -- Prevent GUI from closing on respawn

-- Create the Frame for the GUI
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 200)  -- Increased height for bottom part
frame.Position = UDim2.new(0.5, -150, 0.5, -100)
frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)  -- White
frame.BorderSizePixel = 0
frame.Visible = true
frame.Parent = screenGui

-- Create the Grey Top Section for the Title
local topSection = Instance.new("Frame")
topSection.Size = UDim2.new(1, 0, 0, 40)
topSection.Position = UDim2.new(0, 0, 0, 0)
topSection.BackgroundColor3 = Color3.fromRGB(169, 169, 169)  -- Grey color
topSection.BorderSizePixel = 0
topSection.Parent = frame

-- Create the Title Label
local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(0.9, 0, 1, 0)  -- Fill most of the top section
titleLabel.Position = UDim2.new(0, 0, 0, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "FOV changer by cool_seagull made for my pookie"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255) -- White text
titleLabel.Font = Enum.Font.FredokaOne
titleLabel.TextScaled = true
titleLabel.Parent = topSection

-- Create the Close Button (X)
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0.1, 0, 1, 0)
closeButton.Position = UDim2.new(0.9, 0, 0, 0)  -- Top-right corner
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)  -- White text
closeButton.Font = Enum.Font.GothamBold
closeButton.TextScaled = true
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Red background
closeButton.BorderSizePixel = 0
closeButton.Parent = topSection

-- Create the Input Box for FOV
local fovInput = Instance.new("TextBox")
fovInput.Size = UDim2.new(0.8, 0, 0, 40)
fovInput.Position = UDim2.new(0.1, 0, 0.35, 0)
fovInput.Text = "enter FOV here pookie"
fovInput.Font = Enum.Font.Gotham
fovInput.TextColor3 = Color3.fromRGB(0, 0, 0)
fovInput.TextScaled = true
fovInput.BackgroundColor3 = Color3.fromRGB(255, 224, 230)
fovInput.BorderSizePixel = 0
fovInput.Parent = frame

-- Create the Send Button
local sendButton = Instance.new("TextButton")
sendButton.Size = UDim2.new(0.4, 0, 0, 40)
sendButton.Position = UDim2.new(0.3, 0, 0.55, 0)
sendButton.Text = "send !! :3"
sendButton.TextColor3 = Color3.fromRGB(0, 0, 0)
sendButton.Font = Enum.Font.GothamBold
sendButton.TextScaled = true
sendButton.BackgroundColor3 = Color3.fromRGB(255, 192, 203)
sendButton.BorderSizePixel = 0
sendButton.Parent = frame

-- Create the Keybind Input Box
local keybindInput = Instance.new("TextBox")
keybindInput.Size = UDim2.new(0.8, 0, 0, 40)
keybindInput.Position = UDim2.new(0.1, 0, 0.75, 0)
keybindInput.Text = "enter new keybind (e.g. 'T')"
keybindInput.Font = Enum.Font.Gotham
keybindInput.TextColor3 = Color3.fromRGB(0, 0, 0)
keybindInput.TextScaled = true
keybindInput.BackgroundColor3 = Color3.fromRGB(224, 224, 224)
keybindInput.BorderSizePixel = 0
keybindInput.Parent = frame

-- Create the Error Message (initially invisible)
local errorMessage = Instance.new("TextLabel")
errorMessage.Size = UDim2.new(0.8, 0, 0, 20)
errorMessage.Position = UDim2.new(0.1, 0, 0.9, 0)
errorMessage.BackgroundTransparency = 1
errorMessage.Text = "insert a number pookie!"
errorMessage.TextColor3 = Color3.fromRGB(255, 0, 0) -- Red text for error
errorMessage.Font = Enum.Font.Gotham
errorMessage.TextScaled = true
errorMessage.Visible = false
errorMessage.Parent = frame

-- Script to change FOV or show error
sendButton.MouseButton1Click:Connect(function()
    local fovValue = tonumber(fovInput.Text)
    
    if fovValue then
        -- If the value is a number, change FOV
        game.Workspace.CurrentCamera.FieldOfView = fovValue
        errorMessage.Visible = false  -- Hide error message
    else
        -- If not a number, show error message
        errorMessage.Visible = true
    end
end)

-- Initial keybind to open/close the GUI
local UIS = game:GetService("UserInputService")
local openKey = Enum.KeyCode.Tab  -- Default keybind

-- Function to change the keybind based on user input
keybindInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local newKey = keybindInput.Text:upper()  -- Get user input and convert to uppercase
        local success, keyCode = pcall(function() return Enum.KeyCode[newKey] end)

        if success then
            openKey = keyCode  -- Update the openKey to the new keybind
            keybindInput.Text = "keybind set to: " .. newKey
        else
            keybindInput.Text = "invalid key! try again"
        end
    end
end)

-- Script to toggle the GUI using the current keybind
UIS.InputBegan:Connect(function(input)
    if input.KeyCode == openKey then
        frame.Visible = not frame.Visible
    end
end)

-- Script to close the GUI when 'X' is clicked
closeButton.MouseButton1Click:Connect(function()
    frame.Visible = false
end)

-- Dragging functionality for the top grey section
local dragging = false
local dragInput, dragStart, startPos

local function update(input)
    local delta = input.Position - dragStart
    frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

topSection.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
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

topSection.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UIS.InputChanged:Connect(function(input)
    if dragging and input == dragInput then
        update(input)
    end
end)
