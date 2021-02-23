# ThirdPerson
public float gravity = -9.81f;
    float Yvelocity;

    public Transform GroundCheck;
    public LayerMask groundMask;

    public CharacterController controller;
    public float speed = 6f;
    public VariableJoystick joystick;

    public float turnSmoothTime = 0.1f;
    float turnSmoothVelocity;
    bool isGrounded;
    public float groundCheckRadius = 0.4f;

    private void Start()
    {
        if (joystick == null)
        {
            joystick = GameObject.FindWithTag("joystick").GetComponent<VariableJoystick>();
        }
    }

    void Update()
    {
        isGrounded = Physics.CheckSphere(GroundCheck.position, groundCheckRadius, groundMask);
        if (isGrounded && Yvelocity < 0)
        {
            Yvelocity = -2f;
        }

        float horizontal = joystick.Horizontal;
        float vertical = joystick.Vertical;
        Vector3 dir = new Vector3(horizontal, 0f, vertical).normalized;
        if (dir.magnitude >= 0.1f)
        {
            float targetAngle = Mathf.Atan2(dir.x, dir.z) * Mathf.Rad2Deg + Camera.main.transform.eulerAngles.y;
            float angle = Mathf.SmoothDampAngle(transform.eulerAngles.y, targetAngle, ref turnSmoothVelocity, turnSmoothTime);
            transform.rotation = Quaternion.Euler(0f, targetAngle, 0f);

            Vector3 moveDir = Quaternion.Euler(0f, targetAngle, 0f) * Vector3.forward;
            controller.Move(moveDir.normalized * speed * Time.deltaTime);
        }
        Yvelocity += gravity * Time.deltaTime;
        controller.Move(new Vector3(0f, Yvelocity * Time.deltaTime, 0f));
    }
    public void Jump()
    {
        if (isGrounded)
        {
            Yvelocity = Mathf.Sqrt(JumpHeight * -2f * gravity);
        }
    }
    public float JumpHeight = 3f;
