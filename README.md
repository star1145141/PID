# PID

​local PIDController = {}
PIDController.__index = PIDController
function PIDController:new(kp, ki, kd, dt, output_min, output_max)
local self = setmetatable({}, PIDController)
self.kp = kp or 1.0
self.ki = ki or 0.0
self.kd = kd or 0.0
self.dt = dt or 0.01
self.output_min = output_min or -math.huge
self.output_max = output_max or math.huge
self.last_error = 0
self.integral = 0
self.last_output = 0
return self
end
function PIDController:set_params(kp, ki, kd)
self.kp = kp
self.ki = ki
self.kd = kd
end
function PIDController:reset()
self.last_error = 0
self.integral = 0
self.last_output = 0
end
function PIDController:update(setpoint, feedback)
local error = setpoint - feedback
local p_term = self.kp  error
self.integral = self.integral + error  self.dt
local i_term = self.ki  self.integral
local derivative = (error - self.last_error) / self.dt
local d_term = self.kd  derivative
local output = p_term + i_term + d_term
if output > self.output_max then
output = self.output_max
self.integral = self.integral - error  self.dt
elseif output < self.output_min then
output = self.output_min
self.integral = self.integral - error  self.dt
end
self.last_error = error
self.last_output = output
return output
end
function PIDController:get_debug_info()
return {
integral = self.integral,
last_error = self.last_error,
last_output = self.last_output
}
end
return PIDController
