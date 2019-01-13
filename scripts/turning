AFRAME.registerComponent("character-controller", {
      schema: {
        pivot: { type: "selector" },
        snapRotationDegrees: { default: THREE.Math.DEG2RAD * 45 },
      },
      init: function() {
        this.pendingSnapRotationMatrix = new THREE.Matrix4();
        this.snapRotateLeft = this.snapRotateLeft.bind(this);
        this.snapRotateRight = this.snapRotateRight.bind(this);
        
        this.el.sceneEl.addEventListener('rotateleft', this.snapRotateLeft);
        this.el.sceneEl.addEventListener('rotateright', this.snapRotateRight);
      },
      update: function() {
        this.leftRotationMatrix = new THREE.Matrix4().makeRotationY(this.data.snapRotationDegrees);
        this.rightRotationMatrix = new THREE.Matrix4().makeRotationY(-this.data.snapRotationDegrees);
      },
      snapRotateLeft: function() {
        this.pendingSnapRotationMatrix.copy(this.leftRotationMatrix);
      },
      snapRotateRight: function() {
        this.pendingSnapRotationMatrix.copy(this.rightRotationMatrix);
      },
      tick: (function() {
        const trans = new THREE.Matrix4();
        const transInv = new THREE.Matrix4();
        const pivotPos = new THREE.Vector3();
        const rotationAxis = new THREE.Vector3(0, 1, 0);
        const rotationMatrix = new THREE.Matrix4();
        const rotationInvMatrix = new THREE.Matrix4();
        const pivotRotationMatrix = new THREE.Matrix4();
        const pivotRotationInvMatrix = new THREE.Matrix4();
        return function(t, dt) {
          const root = this.el.object3D;
          const pivot = this.data.pivot.getObject3D('camera');
          root.updateMatrix();
            pivotPos.copy(pivot.position);
            pivotPos.applyMatrix4(root.matrix);
            trans.setPosition(pivotPos);
            transInv.makeTranslation(-pivotPos.x, -pivotPos.y, -pivotPos.z);
            rotationMatrix.makeRotationAxis(rotationAxis, root.rotation.y);
            rotationInvMatrix.makeRotationAxis(rotationAxis, -root.rotation.y);
            pivotRotationMatrix.makeRotationAxis(rotationAxis, pivot.rotation.y);
            pivotRotationInvMatrix.makeRotationAxis(rotationAxis, -pivot.rotation.y);
            // Translate to middle of playspace (player rig)
            root.matrix.premultiply(transInv);
            // Zero playspace (player rig) rotation
            root.matrix.premultiply(rotationInvMatrix);
            // Apply snap rotation if necessary
            root.matrix.premultiply(this.pendingSnapRotationMatrix);
            // Reapply playspace (player rig) rotation
            root.matrix.premultiply(rotationMatrix);
            // Reapply playspace (player rig) translation
            root.matrix.premultiply(trans);
            // update pos/rot/scale
            root.matrix.decompose(root.position, root.quaternion, root.scale);
            this.pendingSnapRotationMatrix.identity(); // Revert to identity
          };
        })(),
      });
