# Simple Mumble Guide

## Notes
This was tested on a locally hosted server and a VPS, both running onesync 32 players.  
We tested this with no more then 3 players at a time, your result might vary.

## File Edits
Add `"voip-server:mumble"` to your `components.json` file (note: Don't ruin the json table. Pay attention to your commas).  
Add `setr voice_use3dAudio true` to your server.cfg (probably anywhere, mine was near the bottom, after resources start).  
Add `setr voice_useSendingRangeOnly true` to your server.cfg (same as above, probably anywhere).  
Ensure you have onesync running on your server (add `+set onesync_enabled true` to your launcher after `+exec server.cfg`).  

## Usage
Add the following script to a client file, and make sure you have no other resources interfering with voice control.  

```
local defaultProximity = 20.0
local inCall = false

Citizen.CreateThread(function()  
  NetworkSetTalkerProximity(defaultProximity)
end)

RegisterCommand('makecall', function(source,args)
  if not args or #args < 2 then 
    print("Call failed.")
    return
  end

  local targetChannel = tonumber(args[1])
  MumbleSetVoiceTarget(targetChannel)

  for i=2,#args,1 do
    local targetPlayer = tonumber(args[i])
    MumbleAddVoiceTargetPlayer(targetChannel,targetPlayer)
  end

  inCall = targetChannel
  print("Call started.")
end)

RegisterCommand('leavecall', function(source,args)
  if inCall then
    MumbleClearVoiceTarget(inCall)
    MumbleSetVoiceTarget(0)

    inCall = false
  end
end)
```

Assuming you done all of the above, you should have 3d spacial sound working correctly. 
Note known issues below if yours is not working correctly at this point, particuarly the part about surround sound headphones.

## Known Issues
- Some people have robo-man voice. No instructions to reproduce yet.
- Some people are uneffected by 3d sound until they activate the surround sound option on their headphones.
  - Known information:
    - Effected target:
      - Windows 10 64-bit home edition
      - Version 1903 build 18362.657
      - G933 wireless 7.1 headset
    - Server information:
      - Windows 10 64 bit pro edition
      - Version 1909 build 18363.657
      - Server build 1972
      - Home hosted
    - Reproduction steps
      - Turn surround sound off on headphones for voice calculations to stop working.
      - Turn surround sound back on and both 3d audio and distance calulations seem to work.
