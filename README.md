# example1
//
//  AnimationSelectorView.swift
//  CollageBuilder
//
//

import SwiftUI

struct AnimationSelectorView: View {
    
    @Binding var animation: AnimationSettings?
    
    @State private var allAnimations = [AnimationData?]()
    @State private var animationData: AnimationData?
    
    var body: some View {
        HStack {
            Text("Animation: ")
            Picker("", selection: $animationData) {
                ForEach(allAnimations, id: \.self) {
                    Text($0?.name ?? "none")
                }
            }
        }
        .onChange(of: animationData) {
            animation = $0?.settings
        }
        .task {
            allAnimations = await AnimationsProvider.allAnimations
            allAnimations.append(nil)
            animationData = allAnimations
                .compactMap { $0 }
                .first(where: {
                    $0.id == animation?.id
            })
        }
    }
}

struct AnimationSelectorView_Previews: PreviewProvider {
    static var previews: some View {
        AnimationSelectorView(animation: .constant(nil))
    }
}
