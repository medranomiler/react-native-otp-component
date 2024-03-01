## Example Usage

```
import { useRef, useState } from "react";
import type { TextInput } from "react-native";
import {
  KeyboardAvoidingView,
  SafeAreaView,
  Text,
  TouchableOpacity,
  TouchableWithoutFeedback,
  View,
} from "react-native";
import { Stack, useRouter } from "expo-router";
import { useSignUp } from "@clerk/clerk-expo";
import { FontAwesome5 } from "@expo/vector-icons";

import { OTPInput } from "~/components/OTPInput";
import { isClerkError } from "~/utils/isClerkError";

export default function PhoneVerificationScreen() {
  const { isLoaded, signUp } = useSignUp();
  const [codes, setCodes] = useState<string[] | undefined>(Array(6).fill(""));
  const refs = Array(6)
    .fill(null)
    .map(() => useRef<TextInput>(null));

  const [errorMessages, setErrorMessages] = useState<string[]>();
  const router = useRouter();

  if (!isLoaded) {
    return <Text>Loading...</Text>;
  }

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

  async function verifyPhoneNumberAndProgress() {
    const fullCode = codes!.join("");
    try {
      await signUp?.attemptPhoneNumberVerification({ code: fullCode });
    } catch (err: unknown) {
      // handle the error 
    }

    router.replace("/profile/sign-up/phone-verified");
  }

  return (
    <SafeAreaView className={"flex h-full w-full flex-1 bg-[#fff]"}>
      <KeyboardAvoidingView>
        <TouchableWithoutFeedback>
          <View className={"flex flex-col gap-y-4 p-4"}>
            <View>
              <Text className={"mb-2 text-center text-2xl font-semibold"}>
                Sign Up
              </Text>
              <Text className={"text-center text-lg"}>1/3</Text>
            </View>
            <View className={"flex flex-col items-center mb-4"}>
              <Text className={"mb-2 text-center text-2xl font-semibold"}>
                Enter verification code
              </Text>
              <Text className={"w-2/3 text-center text-lg"}>
                We just texted the code to your phone number
              </Text>
            </View>
            <OTPInput
              codes={codes!}
              errorMessages={errorMessages}
              onChangeCode={onChangeCode}
              refs={refs}
            />
     // the rest of the code
     </KeyboardAvoidingView>
    </SafeAreaView>
  );
}
```