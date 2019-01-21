Stackoverflow question      
Title: AFRAME my-laser-component + thumb controls = click function does not seem to work
I combined the thumb controls component with the my laser controls component. So that the laser controls could be mapped to the thumbupstart event, and also so that laser controls would NOT be mapped to the whole trackpad down. After implementing the code, and integrating a debug panel in VR, I discovered that the thumbupstart is being listened to, but for some unknown reason it�s not creating a click event.
I have previously copied the entire AFRAME stack into a JS file and then edited the daydream controller laser component start event, and that worked, in so far as the laser control emits a click event when the thumbupstart is pressed, but the laser control does not emit a click when thumbleftstart, thumbrightstart, thumbupstart, or trackpaddown event is triggered. This works great, but it�s the entire aframe stack in a js file. That doesn�t seem like the right way to do it.
I have tried several times to get it working as a component, so that only the thumbupstart area of the trackpad would create a click event.
http://thundering-velvet.glitch.me this code includes an in VR debug panel that shows that the thumbupstart is being listened to by the a-scene, however when it is pressed it does not trigger click events on the one object in the a-scene that will animate when clicked.

Answer:
You need to tell the cursor to listen to the custom events that it doesn't know anything about.

cursor="downEvents: thumbupstart; upEvents: thumbupend"

Both the down and up are required to trigger the click event. Alternatively, you can just create the click event yourself by manually checking.

el.addEventListener('thumbupstart', () => {
  const intersected = el.components.cursor.intersectedEl;
  if (intersected) { 
    intersected.emit('click');
    el.emit('click');
  }
});

shareimprove this answer
answered days ago
ngokevin
9,02611457
