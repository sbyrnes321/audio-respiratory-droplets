# Improving COVID-19 digital contact tracing by estimating respiratory droplets

(Contributions welcome!)

# FAQ

## What is digital contact tracing?

See [this comic strip introduction](https://ncase.me/contact-tracing/). The most important digital-contact-tracing system is likely to be the [upcoming Apple-Google OS update](https://www.apple.com/covid19/contacttracing/), but there are various others like [NOVID](https://www.novid.org/), Singapore's TraceTogether, etc.

## What does it mean to make a digital contact tracing system "work better", and why is it important?

A key aspect of digital contact tracing is estimating transmission probability between two people—i.e., if Alice and Bob are near each other, and Alice is infectious, how likely is it that Bob will catch it from Alice? Existing systems estimate this quantity by combining distance and duration. Distance is estimated typically via Bluetooth signal strength ("RSSI"), with stronger signals indicating closer phones. [NOVID](https://www.novid.org/) uses a different scheme based on the speed of sound (one phone emits a high-pitched beep every 5 minutes, the other phone times the exact moment when the beep arrives).

Distance and duration do not add up to a perfect proxy for transmission probability, and likewise Bluetooth RSSI is not a perfect proxy for distance. There are going to be both false alarms and misses. **If there are too many misses**, people get sick and die, and/or we need to compensate with more aggressive society-wide lock-downs etc. Conversely, **if there are too many false alarms**, people will stop complying with the endless quarantine requests that don’t end in them getting sick. Either they’ll figure that they must have already caught it and now they’re immune, or they’ll lose faith in the systems, or they may even simply run out of ability to find substitutes for their jobs and other obligations etc.

If we lower the threshold for exposure notifications, then we reduce misses, but increase false alarms. If we raise the threshold, it's the other way around. **We "make a system work better" by pushing up the ROC curve, so that we can reduce misses *and* false alarms simultaneously, rather than needing to trade off between them.**

## How can we use audio estimation of respiratory droplets to improve the systems?

* There seems to be widespread consensus—from CDC, WHO, epidemiologists, etc.—that the major transmission vector is when one person creates respiratory droplets and another person breathes them in.

* There also seems to be universal consensus that people emit dramatically more respiratory droplets when they are vocalizing (talking, coughing, singing, laughing, sneezing, etc.) then when they are just quietly breathing. I especially like [this little video](https://www.youtube.com/watch?v=VmYuCtPZRNY). This is also consistent with the fact that [superspreader events generally involve loud talking and singing](https://quillette.com/2020/04/23/covid-19-superspreader-events-in-28-countries-critical-patterns-and-lessons/).

As the CDC says, the virus spreads ["through respiratory droplets produced when an infected person coughs, sneezes, or talks"](https://www.cdc.gov/coronavirus/2019-ncov/prevent-getting-sick/how-covid-spreads.html). The difference in respiratory droplets between talking and simple breathing is supposed to be [a factor of 10](https://www.erinbromage.com/post/the-risks-know-them-avoid-them).

Now, in some situations, a cellphone microphone can easily tell that nobody is vocalizing in a room. So in those situations, we can raise the threshold for "exposure". Conversely, a cellphone microphone might hear loud singing, shouting, coughing, etc., and can set the threshold especially low. With normal conversation, it could be somewhere in between. This will make the systems work better.

## Would this proposal compromise privacy?

As best as I can tell (and I'm not an expert), it doesn’t undercut privacy at all, at least for the decentralized, privacy-centric systems similar to the one [this comic strip is describing](https://ncase.me/contact-tracing/), a category that includes the [Apple-Google system](https://www.apple.com/covid19/contacttracing/) and [NOVID](https://www.novid.org/). The main reason is: the microphone-derived data would be stored locally on the phone and never broadcast to anyone ever.

The privacy concerns with these systems are not about what information is *stored within the phone app*, but rather what information is *broadcast to the government or others*.

The most important and most clear-cut case will be the contact-tracing OS update written by Apple and Google themselves. If you have a privacy concern regarding how much information is accessible to OS software written by Apple and Google, then of course you better throw your phone in the garbage: The phone OS has access to absolutely everything, and we have always been trusting Apple and Google to be good stewards of that.

Secondarily, we are calculating and storing a measure of whether and how loud people were talking, but we are not storing the audio itself, or any information about who was talking or what they said.

## Would this proposal compromise phone battery life?

I'm not an expert, but I'm cautiously optimistic. I see two paths.

Most plausibly, we can duty-cycle—for example, process a 1-second sample of audio every 60 seconds. That would still be better than nothing. Remember, the threshold for declaring a contact is typically 15 minutes, so there would be 15 opportunities to catch someone speaking, if anyone is speaking. And if people are speaking very occasionally, well, that's less respiratory droplets than if people are speaking near-continuously. So classifying that as "low respiratory droplets" is not necessarily wrong.

More ambitiously, the analysis could be done on the phone's low-power coprocessor. [That's how the "Hey Siri" functionality works](https://www.cultofmac.com/509163/apple-reveals-ai-magic-behind-hey-siri/). I think this would require more development effort, so is probably unlikely to happen.

## How do you tell TV or music from live people?

I don't think it's feasible, but I also don't think it's necessary. So if people are quietly watching TV together, they would get a lower threshold even though there are few respiratory droplets in the room. We're not going for perfection, we're just trying to make it less bad. When there *is* talking, you don’t know whether it's the TV or a person. But if there *isn't* talking, and if the environment is quiet enough that you *would* be able to hear talking if it were happening, then you *know* that nobody is talking. So that's a case where you can pretty safely raise the exposure duration threshold, to reduce false alarms without excessively increasing misses.

There are many examples where this would come up: every day people spend time in trains where no one is talking, waiting rooms where no one is talking, offices and laboratories where no one is talking, etc. etc.

## Does software for this exist? What exactly should it do?

Not at the moment, as far as I can tell. There *is* [code for "voice activity detection"](https://github.com/topics/voice-activity-detection), which is kinda close, and could perhaps be modified to solve the problem.

The simplest specification would be a binary classifier along the lines of:

* If the environment is so loud that you can't tell whether or not people are talking, return HIGH RESPIRATORY DROPLETS

* If you hear talking, coughing, laughing, singing, etc., return HIGH RESPIRATORY DROPLETS

* Otherwise, return LOW RESPIRATORY DROPLETS.

We can try to do better than a binary classifier—for example, quiet talking is not as bad as loud talking or singing or coughing—but this would be a start.

## What's the status? How can I help?

Well, at the end of the day, (1) we need working software, and (2) we need the people in charge of deciding contact-tracing systems to decide to use it. Anything that helps on either of those, directly or indirectly, would be great. This includes making the software, figuring out methods of validating it, resolving uncertainties (e.g. about battery-life consequences), outreach / PR, and so on.
