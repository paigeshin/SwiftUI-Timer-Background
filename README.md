![Screen Shot 2021-12-13 at 10.07.55 PM.png](1.png)

```swift
//
//  ContentView.swift
//  SwiftBackgroundTaskTimer
//
//  Created by paige on 2021/12/13.
//

import SwiftUI
import UserNotifications

struct ContentView: View {
    var body: some View {
        Home()
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

struct Home: View {

    @State var start = false
    @State var to: CGFloat = 0
    @State var count = 0
    @State var time = Timer.publish(every: 1, on: .main, in: .common).autoconnect()
    var body: some View {

        ZStack {

            Color.black.opacity(0.06).edgesIgnoringSafeArea(.all)

            VStack {
                ZStack {

                    Circle()
                        .trim(from: 0, to: 1)
                        .stroke(.black.opacity(0.09),
                                style: StrokeStyle(lineWidth: 35, lineCap: .round))
                        .frame(width: 280, height: 280)

                    Circle()
                        .trim(from: 0, to: self.to)
                        .stroke(.red,
                                style: StrokeStyle(lineWidth: 35, lineCap: .round))
                        .frame(width: 280, height: 280)
                        .rotationEffect(.init(degrees: -90))

                    VStack {

                        Text("\(self.count)")
                            .font(.system(size: 65))
                            .fontWeight(.bold)

                        Text("Of 15")
                            .font(.title)

                    } //: VSTACK - TIMER TEXT

                } //: ZSTACK - TIMER WRAPPER

                HStack(spacing: 20) {

                    Button {

                        if self.count == 15 {
                            self.count = 0
                            withAnimation(.default) {
                                self.to = 0
                            }
                        }
                        self.start.toggle()

                    } label: {
                        HStack(spacing: 15) {
                            Image(systemName: self.start ? "pause.fill" : "play.fill")
                                .foregroundColor(.white)

                            Text(self.start ? "Pause" : "Play")
                                .foregroundColor(.white)
                        }
                    }
                    .padding(.vertical)
                    .frame(width: (UIScreen.main.bounds.width / 2) - 55)
                    .background(.red)
                    .clipShape(Capsule())
                    .shadow(radius: 6)

                        Button {

                            self.count = 0

                            withAnimation {
                                self.to = 0
                            }

                        } label: {
                            HStack(spacing: 15) {
                                Image(systemName: "arrow.clockwise")
                                    .foregroundColor(.red)

                                Text("Restart")
                                    .foregroundColor(.red)
                            }
                        }
                        .padding(.vertical)
                        .frame(width: (UIScreen.main.bounds.width / 2) - 55)
                        .background(
                            Capsule()
                                .stroke(.red, lineWidth: 2)
                        )
                        .shadow(radius: 6)
                } //: HSTACK - BUTTON WRAPPER
                .padding(.top, 55)
            } //: VSTACK - TIMER

        } //: VSTACK - ENTIRE PAGE
        .onAppear(perform: {
            UNUserNotificationCenter.current().requestAuthorization(options: [.badge, .sound, .alert]) { _, _ in

            }
        })
        .onReceive(self.time) { _ in
            if self.start {
                if self.count != 15 {
                    self.count += 1
                    withAnimation(.default) {
                        self.to = CGFloat(self.count) / 15
                    }
                } else {
                    withAnimation(.default) {
                        self.to = 0
                    }
                    self.start.toggle()
                    //MARK: - notify
                    self.notify()
                }
            }
        }

    }

    private func notify() {
        let content = UNMutableNotificationContent()
        content.title = "Message"
        content.body = "Timer is completed successfully in background !!!"

        let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 1, repeats: false)
        let req = UNNotificationRequest(identifier: "MSF", content: content, trigger: trigger)

        UNUserNotificationCenter.current().add(req, withCompletionHandler: nil)
    }

}
```
