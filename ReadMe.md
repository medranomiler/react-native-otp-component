## Example Usage

```
import { Text, Button } from "react-native";
import { TextInput } from "react-native";
import { useRef, useState } from "react";
import { View } from "../../components/Themed";
import { OTPInput } from "react-native-otp-component";

export default function SignIn() {
  const [codes, setCodes] = useState<string[] | undefined>(Array(6).fill(""));
  const refs = Array(6)
    .fill(null)
    .map(() => useRef<TextInput>(null));

  const [errorMessages, setErrorMessages] = useState<string[]>();


  const onChangeCode = (text: string, index: number) => {
    if (text.length > 1) {
      setErrorMessages(undefined);
      const newCodes = text.split("");
      setCodes(newCodes);
      refs[5]!.current?.focus();
      return;
    }
    setErrorMessages(undefined);
    const newCodes = [...codes!];
    newCodes[index] = text;
    setCodes(newCodes);
    if (text !== "" && index < 5) {
      refs[index + 1]!.current?.focus();
    }
  };

  const otpConfig = {
    borderColor: "#fff",
    backgroundColor: "#fff",
    textColor: "#000",
    errorColor: "#dc2626",
    focusColor: "#22c55e"
  }

  return (
    <View style={{height: "100%", width: "100%", justifyContent: "center", alignItems: "center"}}>
      <Text>Enter the verification code</Text>
      <OTPInput
        codes={codes!}
        errorMessages={errorMessages}
        onChangeCode={onChangeCode}
        refs={refs}
        config={otpConfig}
      />
      <Button title="Enter" onPress={()=> {
        const fullCode = codes?.join('')
        console.error(fullCode)
      }}/>
    </View>
  );
}
```